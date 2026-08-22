
# Daniel Mendes — Blog & Portfólio Estático

Site estático e blog profissional desenvolvido com **[Roq](https://iamroq.dev)**, um poderoso Static Site Generator (SSG) construído sobre o ecossistema **[Quarkus](https://quarkus.io)** e templates **Qute**.

Inspirado nos layouts e portfólios modernos para desenvolvedores ([arthurbazzz.dev](https://www.arthurbazzz.dev/) e [guisaliba.github.io](https://guisaliba.github.io/)).

---

## 🚀 Como Executar Localmente

### Opção 1: Via Maven Wrapper (sem instalar CLI adicional)
```bash
# Modo de desenvolvimento com Live-Reload instantâneo
./mvnw quarkus:dev
```
Acesse no navegador: **http://localhost:8080**

### Opção 2: Via Roq CLI
```bash
# Instalação do Roq CLI (caso não tenha instalado)
curl -Ls https://sh.jbang.dev | bash -s - app install --fresh --force roq@quarkiverse/quarkus-roq

# Iniciar o servidor de desenvolvimento
roq start

# Gerar os arquivos estáticos de produção
roq generate

# Pré-visualizar o site estático gerado
roq serve
```

---

## 📦 Como Gerar o Build Estático de Produção

Para gerar todos os arquivos HTML, CSS e JavaScript estáticos na pasta `target/roq/`:

```bash
QUARKUS_ROQ_GENERATOR_BATCH=true ./mvnw -B package quarkus:run
```

Os arquivos gerados em `target/roq/` estão prontos para deploy direto no **GitHub Pages**, **Vercel**, **Netlify** ou qualquer servidor web (Nginx, Apache, Cloudflare Pages).

---

## 📁 Estrutura do Projeto

```
├── config/
│   └── application.properties       # Configurações do Roq e URL do site
├── content/                         # Páginas e Coleções do Blog
│   ├── index.html                   # Página inicial com Hero e Terminal Neofetch
│   ├── blog.html                    # Listagem paginada de artigos
│   ├── about.md                     # Página "Sobre Mim" (Biografia, Filosofia)
│   ├── curriculo.html               # Currículo completo com botão de impressão/PDF
│   ├── projetos.html                # Showcase de projetos e links do GitHub
│   ├── posts/                       # Diretório de artigos do blog
│   │   ├── 2026-08-20-.../index.md  # Artigo sobre Quarkus
│   │   ├── 2026-08-10-.../index.md  # Artigo sobre Virtual Threads
│   │   └── 2026-07-25-.../index.md  # Artigo sobre Clean Architecture
│   ├── rss.xml                      # Feed RSS automático
│   ├── sitemap.xml                  # Sitemap XML para SEO
│   └── 404.html                     # Página de erro customizada
├── data/
│   ├── authors.yml                  # Dados do autor (nome, bio, avatar, redes)
│   └── menu.yml                     # Itens do menu de navegação
├── public/                          # Arquivos estáticos servidos diretamente (imagens, logos, PDFs)
├── web/
│   └── _custom.css                  # Estilização Tailwind CSS v4, tema escuro, terminal, etc.
└── pom.xml                          # Dependências Quarkus e plugins Roq
```

---

## ✍️ Como Criar uma Nova Postagem no Blog

1. Crie uma pasta dentro de `content/posts/` com a data e o slug:
   ```bash
   mkdir -p content/posts/2026-09-01-meu-novo-artigo
   ```
2. Crie o arquivo `index.md` com o cabeçalho FrontMatter:
   ```markdown
   ---
   title: "Título do Meu Novo Artigo"
   description: "Uma breve descrição para SEO e cards de preview."
   slug: meu-novo-artigo
   tags: [java, quarkus, arquitetura]
   author: daniel
   date: 2026-09-01 10:00:00
   ---

   Escreva aqui o conteúdo do seu artigo em Markdown puro...
   ```
3. O artigo será automaticamente incluído no blog, na página inicial e nas páginas de tags correspondentes!

---

## 🌐 Links e Redes
- **LinkedIn:** [https://www.linkedin.com/in/danielmends/](https://www.linkedin.com/in/danielmends/)
- **GitHub:** [https://github.com/danielmendss](https://github.com/danielmendss)

