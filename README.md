<div align="center">

<img src="./public/logo.png" alt="Gen-V logo" width="96" />

# Gen-V

### Prompt → Generate → Preview → Export

<p>
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&size=18&pause=1200&color=10B981&center=true&vCenter=true&width=720&lines=AI-powered+Next.js+application+generator.;Describe+your+idea.+Generate+the+code.;Preview%2C+iterate%2C+and+export+your+projects." alt="Animated Gen-V description" />
</p>

<p>
  <img src="https://img.shields.io/badge/Next.js-16.2.12-000000?logo=next.js&logoColor=white" alt="Next.js" />
  <img src="https://img.shields.io/badge/React-19.2.4-149eca?logo=react&logoColor=white" alt="React" />
  <img src="https://img.shields.io/badge/Prisma-5.22.0-2D3748?logo=prisma&logoColor=white" alt="Prisma" />
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?logo=postgresql&logoColor=white" alt="PostgreSQL" />
  <img src="https://img.shields.io/badge/Clerk-6C47FF?logo=clerk&logoColor=white" alt="Clerk" />
  <img src="https://img.shields.io/badge/Inngest-10B981?logoColor=white" alt="Inngest" />
  <img src="https://img.shields.io/badge/E2B-FF6B35?logoColor=white" alt="E2B" />
  <img src="https://img.shields.io/badge/OpenRouter-111827?logoColor=white" alt="OpenRouter" />
  <img src="https://img.shields.io/badge/Tailwind_CSS-06B6D4?logo=tailwindcss&logoColor=white" alt="Tailwind CSS" />
  <img src="https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white" alt="Docker" />
</p>

<p>
  <strong>Gen-V is an AI-powered application generator that turns natural-language ideas into full-stack Next.js applications.</strong>
</p>

</div>

---

## ✨ What is Gen-V?

Gen-V lets you describe an application in plain language and uses an AI coding agent to build the project inside an isolated sandbox.

The generated application can be previewed live, inspected file-by-file, iterated through follow-up prompts, downloaded as a ZIP, or exported directly to GitHub.

> **Repository status:** Gen-V is an actively developed learning/product project. The complete experience requires external services and environment variables described below.

---

## 🚀 Features

### 🧠 AI Application Generation

- Describe an application using natural-language prompts.
- Multi-step AI coding agent with tool calling.
- Generates and modifies files inside an isolated E2B sandbox.
- Supports configurable AI providers.
- OpenRouter can use model fallbacks when a provider/model is unavailable.

### 🖥️ Project Workspace

- Persistent project history backed by PostgreSQL and Prisma.
- Chat-style generation history.
- Live E2B preview.
- Demo and Code views.
- File explorer with tree navigation.
- Syntax-highlighted code viewer.
- Copy generated code.
- Follow-up prompts for iterative development.

### 📦 Project Management

- Create projects from prompts.
- Automatically derive project names.
- Rename projects.
- Duplicate projects.
- Favorite/unfavorite projects.
- Delete projects.
- Search, filter, and sort projects.

### 📤 Export

- Download generated projects as ZIP files.
- Export a generated version to GitHub.
- Create or reuse a GitHub repository.
- Generated project files are retained in Prisma so they remain available after an E2B sandbox expires.

### 🔐 Authentication & Usage

- Clerk authentication.
- Sign in and sign up.
- Protected project routes.
- Clerk-to-Prisma user synchronization.
- Plan-based usage limits.
- Current usage defaults:
  - Free plan: **5 points**
  - Pro plan: **100 points**
  - One generation: **1 point**

---

## 🧭 Application Routes

| Route | Access | Purpose |
| --- | --- | --- |
| `/` | Public / authenticated | Dashboard, project creation, templates, project list |
| `/sign-in` | Public | Clerk sign-in |
| `/sign-up` | Public | Clerk sign-up |
| `/pricing` | Public | Pricing page |
| `/projects/[projectsId]` | Protected | Chat, preview, code explorer, and exports |
| `/api/inngest` | Integration | Inngest background agent endpoint |

`src/proxy.js` protects non-public routes with Clerk middleware.

---

## 🏗️ Architecture

```mermaid
flowchart LR
    U[User Prompt] --> N[Next.js UI]
    N --> C[Clerk Auth]
    N --> S[Server Actions]
    S --> P[(PostgreSQL)]
    S --> I[Inngest]
    I --> A[AI Coding Agent]
    A --> E[E2B Sandbox]
    E --> R[Live Preview]
    A --> F[Generated Files]
    F --> P
    F --> Z[ZIP Export]
    F --> G[GitHub Export]
    R --> N
```

### Main Folders

| Folder | Responsibility |
| --- | --- |
| `src/app/` | Next.js App Router pages, layouts, loading states, and API routes |
| `src/modules/home/` | Dashboard, project list, prompt form, toolbar, and navigation |
| `src/modules/projects/` | Project workspace, preview, code explorer, and exports |
| `src/modules/messages/` | Message actions, chat UI, and generation states |
| `src/modules/auth/` | Clerk/Prisma onboarding and user lookup |
| `src/modules/usage/` | Usage actions, hooks, and credit indicator |
| `src/inngest/` | Inngest client, agent functions, provider selection, and result parsing |
| `src/components/` | Shared UI components, providers, themes, and AI rendering |
| `src/lib/` | Database client, usage limiter, project naming, and utilities |
| `prisma/` | PostgreSQL schema and migrations |
| `sandbox-templates/next-js/` | E2B Next.js template and sandbox build scripts |
| `public/` | Static assets and Gen-V branding |

---

## 🗃️ Data Model

The Prisma schema contains five core models:

| Model | Purpose |
| --- | --- |
| `User` | Local profile linked to a Clerk user through `clerkId` |
| `Project` | User-owned project workspace |
| `Message` | User prompts and assistant responses |
| `Fragment` | Generated version containing files, title, preview URL, and project snapshot |
| `Usage` | Plan-based usage/rate-limiter state |

Project ownership checks are applied to project operations, fragment downloads, and GitHub exports.

---

## 🛠️ Tech Stack

<div align="center">

| Technology | Purpose |
| --- | --- |
| **Next.js** | Full-stack React framework |
| **React** | User interface |
| **Tailwind CSS** | Styling |
| **Prisma** | Database ORM |
| **PostgreSQL** | Persistent database |
| **Clerk** | Authentication and plans |
| **Inngest** | Background AI workflows |
| **E2B** | Isolated code-generation sandbox |
| **AI SDK** | AI model and tool integration |
| **OpenRouter** | Recommended AI provider |
| **Docker** | Local PostgreSQL environment |
| **GitHub API** | Project export |

</div>

---

## 🧑‍💻 Local Setup

### Prerequisites

- Node.js 22+
- npm
- Docker Desktop or a Docker-compatible runtime
- Clerk application credentials
- AI provider API key
- E2B API key
- GitHub personal access token if GitHub export is required

### 1. Clone the repository

```bash
git clone https://github.com/manishmotirale/Gen-V.git
cd Gen-V
```

### 2. Install dependencies

```bash
npm install
```

### 3. Configure environment variables

Create a `.env` file in the project root.

```env
DATABASE_URL="postgresql://postgres:postgres@localhost:5431/postgres"

NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY="pk_test_..."
CLERK_SECRET_KEY="sk_test_..."

NEXT_PUBLIC_CLERK_SIGN_IN_URL="/sign-in"
NEXT_PUBLIC_CLERK_SIGN_UP_URL="/sign-up"

AI_PROVIDER="openrouter"
OPENROUTER_API_KEY="..."

# Optional providers:
# GROQ_API_KEY="..."
# CEREBRAS_API_KEY="..."
# GEMINI_API_KEY="..."

E2B_API_KEY="..."
```

> Never commit `.env` files or expose secret keys in source code.

### 4. Start PostgreSQL

```bash
docker compose up -d
```

The included Docker configuration exposes PostgreSQL on:

```text
localhost:5431
```

### 5. Apply Prisma migrations

```bash
npx prisma migrate dev
```

If necessary:

```bash
npx prisma generate
```

### 6. Start Gen-V

```bash
npm run dev
```

Open:

```text
http://localhost:3000
```

---

## 🤖 AI Provider Selection

Set `AI_PROVIDER` to one of the supported providers.

| Provider | Value | Environment Variable |
| --- | --- | --- |
| OpenRouter | `openrouter` | `OPENROUTER_API_KEY` |
| Groq | `groq` | `GROQ_API_KEY` |
| Cerebras | `cerebras` | `CEREBRAS_API_KEY` |
| Google Gemini | `gemini` | `GEMINI_API_KEY` |

OpenRouter is configured as the recommended provider because its model fallback configuration can help recover from provider rate limits or unavailable models.

---

## 📦 Available Scripts

| Command | Description |
| --- | --- |
| `npm run dev` | Starts the development environment |
| `npm run build` | Creates a production build |
| `npm run start` | Starts the production server |
| `npm run lint` | Runs ESLint |
| `npx prisma migrate dev` | Applies local Prisma migrations |
| `npx prisma generate` | Generates Prisma Client |
| `npx prisma studio` | Opens Prisma Studio |

Before opening a pull request:

```bash
npm run lint
npm run build
```

---

## 🧪 Quick First Run

After signing in:

1. Enter an application idea in the dashboard.
2. Submit the prompt.
3. Wait for the AI agent to generate the project.
4. Open the project workspace.
5. View the generated application in the live preview.
6. Switch to **Code** to inspect the generated files.
7. Send a follow-up prompt to iterate.
8. Download the project as a ZIP or export it to GitHub.

---

## ⚠️ Operational Notes

- **E2B previews are ephemeral.** A sandbox may expire and make an older preview unavailable.
- **Generated files are persistent.** Gen-V stores generated files and project snapshots separately from the sandbox.
- **Docker is required** for the default local PostgreSQL setup.
- PostgreSQL uses host port **5431**, not the default `5432`.
- **Generation consumes usage credits.**
- GitHub export requires appropriate repository creation/content permissions.
- GitHub tokens are used for the export request and are not persisted by Gen-V.
- Keep `.next/`, `node_modules/`, `.env*`, sandbox output, and other generated files out of Git commits.

---

## 🔐 Security

Gen-V follows several security boundaries:

- Clerk middleware protects authenticated routes.
- Server actions verify the current authenticated user.
- Project queries are scoped to the owning user.
- Fragment exports are scoped through the owning project.
- Project deletion requires both project ID and authenticated user ID.
- AI provider keys remain server-side.
- Database credentials remain in environment variables.
- GitHub tokens are handled only for the export request.

---

## 🗺️ Development Flow

```text
User
  ↓
Natural-language prompt
  ↓
Next.js application
  ↓
Inngest event
  ↓
AI coding agent
  ↓
E2B sandbox
  ↓
Generated Next.js application
  ↓
Live preview + stored files
  ↓
Iterate / Download / GitHub Export
```

---

## 📸 Project Preview

Add screenshots or a short demo GIF here:

```text
docs/
├── dashboard.png
├── project-workspace.png
├── code-view.png
└── demo.gif
```

Example:

```md
![Gen-V Dashboard](./docs/dashboard.png)
```

---

## 📚 Useful References

- [Next.js Documentation](https://nextjs.org/docs/app)
- [Prisma Documentation](https://www.prisma.io/docs)
- [Clerk Documentation](https://clerk.com/docs/quickstarts/nextjs)
- [Inngest Documentation](https://www.inngest.com/docs)
- [E2B Documentation](https://e2b.dev/docs)
- [OpenRouter Documentation](https://openrouter.ai/docs)
- [GitHub REST API](https://docs.github.com/en/rest)

---

## 📄 License

No license file is currently included.

If you plan to distribute Gen-V publicly, add an explicit open-source license to the repository.

---

<div align="center">

### Built with Next.js, Prisma, Clerk, Inngest, E2B, and a lot of prompts.

<p>
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&size=14&pause=1600&color=10B981&center=true&vCenter=true&width=560&lines=Build+ideas.;Generate+applications.;Iterate+faster.;Ship+with+Gen-V." alt="Animated Gen-V footer" />
</p>

⭐ If you find Gen-V useful, consider giving the repository a star.

</div>
