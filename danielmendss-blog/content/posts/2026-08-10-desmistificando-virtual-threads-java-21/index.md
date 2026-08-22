---
title: "Desmistificando Virtual Threads no Java 21+: Alta concorrência sem complexidade"
description: "Como as Virtual Threads do Project Loom funcionam por baixo dos panos e quando utilizá-las no lugar de modelos reativos ou thread pools tradicionais."
slug: desmistificando-virtual-threads-java-21
tags: [java, concorrencia, backend, loom, virtual-threads]
author: daniel
date: 2026-08-10 10:00:00
---

A introdução das **Virtual Threads** no Java 21 (fruto do ambicioso *Project Loom*) representa uma das maiores evoluções na história da plataforma Java desde a introdução dos Generics no Java 5 e Lambdas no Java 8.

Mas o que são exatamente as Virtual Threads, que problema elas resolvem e quando devemos (ou não) utilizá-las?

---

## 🧵 O Problema do Modelo Tradicional (Platform Threads)

Historicamente, cada thread criada na JVM (`java.lang.Thread`) possuía um mapeamento direto de 1 para 1 com uma **Thread do Sistema Operacional** (OS Thread ou Platform Thread).

As Platform Threads são recursos caros:
- Cada thread aloca cerca de **1MB de stack memory**.
- O número máximo de threads ativas em um servidor costuma ficar limitado a algumas centenas ou poucos milhares (ex: 200 a 1000).
- Trocas de contexto (*context switching*) gerenciadas pelo kernel do SO consomem preciosos ciclos de CPU.

Em aplicações web baseadas em **Thread-per-Request** (como Spring MVC tradicional ou JAX-RS com pools de workers), quando a aplicação faz uma chamada I/O bloqueante (consulta a banco de dados, chamada HTTP a uma API externa ou leitura de arquivo em disco), a thread do SO fica **ociosa e bloqueada**, desperdiçando recursos.

---

## 💡 A Revolução das Virtual Threads

As Virtual Threads são threads leves gerenciadas **diretamente pela JVM**, e não pelo Sistema Operacional.

```
+-----------------------------------------------------------+
|              Milhões de Virtual Threads                   |
+-----------------------------------------------------------+
                           │ (Mapeamento M : N)
+-----------------------------------------------------------+
|     Poucas Carrier Threads (Platform Threads da JVM)       |
+-----------------------------------------------------------+
                           │ (Mapeamento 1 : 1)
+-----------------------------------------------------------+
|               Kernel Threads do Sistema Operacional        |
+-----------------------------------------------------------+
```

### Como funciona na prática?
1. Uma Virtual Thread é vinculada a uma **Carrier Thread** (thread do SO) para executar código CPU.
2. Quando a Virtual Thread executa uma operação de I/O bloqueante (como `socket.read()` ou `database.query()`), a JVM intercepta essa chamada.
3. A Virtual Thread é **desmontada** (*unmounted*) da Carrier Thread, salvando apenas o seu estado na heap (poucos kilobytes).
4. A Carrier Thread fica imediatamente livre para executar outra Virtual Thread!
5. Quando o I/O finaliza, o sistema operacional notifica a JVM, que remonta a Virtual Thread em qualquer Carrier Thread disponível.

---

## ⚙️ Exemplo Prático de Código

Criar e usar Virtual Threads no Java 21 é surpreendentemente simples:

```java
try (var executor = Executors.newVirtualThreadPerTaskExecutor()) {
    IntStream.range(0, 10_000).forEach(i -> {
        executor.submit(() -> {
            Thread.sleep(Duration.ofSeconds(1)); // I/O simulado
            return i;
        });
    });
} // O try-with-resources aguarda automaticamente todas as tasks
```

Executar 10.000 ou até 1.000.000 de tarefas concorrentes dessa forma consome uma fração insignificante de memória e não esgota as threads do sistema operacional.

---

## 🧭 Boas Práticas e Recomendações

- **Não crie pools de Virtual Threads:** Como elas são extremamente baratas de instanciar e destruir, você não deve usar `ThreadPoolExecutor` para reutilizá-las. Crie uma nova virtual thread para cada tarefa concorrente.
- **Cuidado com `synchronized` (Pinning):** Blocos `synchronized` em código legado podem prender (*pin*) a virtual thread à carrier thread durante I/O. Prefira `java.util.concurrent.locks.ReentrantLock`.
- **Use para I/O-bound tasks:** Virtual Threads brilham em aplicações I/O-bound (APIs web, microsserviços, chamadas HTTP, bancos de dados). Para tarefas puramente CPU-bound (como renderização pesada ou criptografia), Platform Threads tradicionais continuam sendo a melhor escolha.

As Virtual Threads trazem de volta a simplicidade e a legibilidade do código síncrono e sequencial, entregando a escalabilidade massiva que antes só era possível com programação reativa complexa.
