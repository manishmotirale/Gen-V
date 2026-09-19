<div align="center">

<img src="./public/logo.png" alt="Gen-V logo" width="96" />

# Gen-V

### Prompt → Generate → Preview → Export

<a href="https://git.io/typing-svg">
  <img src="https://readme-typing-svg.demolab.com?font=JetBrains+Mono&size=18&pause=1000&color=10B981&center=true&vCenter=true&width=620&lines=Describe+an+idea.+Get+a+working+Next.js+app.;Iterate+with+an+AI+coding+agent+in+seconds.;Preview+your+generation+and+export+the+code." alt="Animated Gen-V product description" />
</a>

<p>
  <img src="https://img.shields.io/badge/Next.js-16.2.12-000000?logo=next.js&logoColor=white" alt="Next.js 16.2.12" />
  <img src="https://img.shields.io/badge/React-19.2.4-149eca?logo=react&logoColor=white" alt="React 19.2.4" />
  <img src="https://img.shields.io/badge/Prisma-5.22.0-2D3748?logo=prisma&logoColor=white" alt="Prisma 5.22.0" />
  <img src="https://img.shields.io/badge/PostgreSQL-Docker-4169E1?logo=postgresql&logoColor=white" alt="PostgreSQL" />
  <img src="https://img.shields.io/badge/AI-Agent-Inngest-10B981" alt="Inngest AI agent" />
  <img src="https://img.shields.io/badge/Sandbox-E2B-FF6B35" alt="E2B sandbox" />
</p>

<p><strong>Gen-V is a v0-style AI app builder for generating, previewing, iterating on, and exporting full-stack Next.js interfaces from natural-language prompts.</strong></p>

</div>

> **Repository status:** This project is an actively developed learning/product build. The README is based on the current implementation and intentionally calls out the services and secrets required to run the complete experience.

## ✨ What Gen-V does

1. **Describe an app** — Start with a prompt such as “Build a modern analytics dashboard with a collapsible sidebar.”
2. **Generate code** — A tool-calling AI agent receives the prompt and works inside an isolated E2B sandbox.
3. **Watch the result** — Gen-V stores the conversation and generated fragment, then exposes a live preview URL.
4. **Inspect and iterate** — Review the assistant’s responses, switch between the **Demo** and **Code** tabs, browse files, and send follow-up instructions.
5. **Keep or export it** — Download the generated project as a ZIP or push a version to a GitHub repository in one commit.

## 🚀 Feature overview

### Project workspace

- Prompt-driven project creation with derived project names.
- Persistent project history backed by PostgreSQL and Prisma.
- Chat-style generation history with assistant results and errors.
- Live E2B preview embedded beside the conversation.
- Code tab with a file explorer, tree navigation, syntax-highlighted code, copy actions, and export controls.
- Project rename, duplicate, favorite, and delete actions.
- Search, filtering, sorting, and favorite views on the project dashboard.

### AI generation pipeline

- Inngest event: `code-agent/run`.
- Multi-step code-agent execution with sandbox file and terminal operations.
- Generated fragments store both the files written by the agent and, when available, a snapshot of the complete sandbox project.
- Configurable AI providers through `src/inngest/model.js`.
- OpenRouter is the recommended default because its model fallback list can recover from provider rate limits or unavailable models.

### Accounts, plans, and usage

- Clerk sign-in, sign-up, route protection, onboarding, and plan checks.
- Prisma-backed usage limiter with a 30-day window.
- Current defaults in `src/lib/usage.js`:
  - Free plan: **5 points**
  - Pro plan: **100 points**
  - One generation: **1 point**
- Pricing is rendered through Clerk’s `<PricingTable />`.

### Export and recovery

- ZIP downloads are generated server-side with JSZip.
- GitHub export creates or reuses a repository and pushes a fragment as one commit.
- GitHub tokens are used for the request and are not persisted by Gen-V.
- Generated project files are retained in Prisma so the code remains useful even after an E2B sandbox expires.

## 🧭 Application routes

| Route | Access | Purpose |
| --- | --- | --- |
| `/` | Public shell / authenticated experience | Dashboard, project creation form, templates, and project list |
| `/sign-in` | Public | Clerk sign-in |
| `/sign-up` | Public | Clerk sign-up |
| `/pricing` | Public | Clerk pricing table |
| `/projects/[projectsId]` | Protected | Chat, live preview, code explorer, and export workspace |
| `/api/inngest` | Integration endpoint | Inngest serves and invokes the background agent functions |

`src/proxy.js` protects non-public routes with Clerk middleware. Static assets and framework internals are excluded from the middleware matcher.

## 🏗️ Architecture at a glance

```mermaid
flowchart LR
    U[User prompt] --> N[Next.js UI]
    N --> C[Clerk auth]
    N --> S[Server actions]
    S --> P[(PostgreSQL)]
    S --> I[Inngest event]
    I --> A[AI code agent]
    A --> E[E2B sandbox]
    E --> R[Live preview URL]
    A --> F[Fragment + file snapshot]
    F --> P
    F --> Z[ZIP export]
    F --> G[GitHub export]
    R --> N
```

### Main folders

| Folder | Responsibility |
| --- | --- |
| [`src/app/`](./src/app) | Next.js App Router layouts, pages, loading state, and Inngest route |
| [`src/modules/home/`](./src/modules/home) | Dashboard, project list, prompt form, toolbar, and navigation |
| [`src/modules/projects/`](./src/modules/projects) | Project workspace, preview, code explorer, project actions, and exports |
| [`src/modules/messages/`](./src/modules/messages) | Message creation, retrieval, chat UI, and generation loading states |
| [`src/modules/auth/`](./src/modules/auth) | Clerk-to-Prisma onboarding and current-user lookup |
| [`src/modules/usage/`](./src/modules/usage) | Usage status actions, hook, and credit indicator |
| [`src/inngest/`](./src/inngest) | Inngest client, agent functions, provider selection, and result parsing |
| [`src/components/`](./src/components) | Shared UI primitives, theme support, providers, and AI response rendering |
| [`src/lib/`](./src/lib) | Prisma client, usage limiter, project naming, and shared utilities |
| [`prisma/`](./prisma) | PostgreSQL schema and migrations |
| [`sandbox-templates/next-js/`](./sandbox-templates/next-js) | E2B Next.js image/template and sandbox build scripts |
| [`public/`](./public) | Static assets, including the Gen-V logo |

## 🗃️ Data model

The Prisma schema in [`prisma/schema.prisma`](./prisma/schema.prisma) contains five core models:

| Model | Role |
| --- | --- |
| `User` | Local profile linked to a Clerk user via `clerkId` |
| `Project` | User-owned workspace, including name and favorite state |
| `Message` | Prompt or assistant result stored in chronological order |
| `Fragment` | Generated version with sandbox URL, title, files, and optional full project snapshot |
| `Usage` | Rate-limiter state for plan-based credits |

Messages and fragments cascade from their project. Ownership checks are applied to project reads, mutations, fragment downloads, and GitHub exports.

## 🛠️ Local setup

### Prerequisites

- Node.js 22 or newer is recommended (the E2B template uses `node:22-slim`).
- npm.
- Docker Desktop or a Docker-compatible runtime.
- Clerk application credentials.
- An AI provider key.
- An E2B API key.
- A GitHub personal access token only if you want GitHub export.

### 1. Install dependencies

```bash
npm install
```

### 2. Configure environment variables

Create a `.env` file in the repository root. Do **not** commit it.

```dotenv
DATABASE_URL="postgresql://postgres:postgres@localhost:5431/postgres"

NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY="pk_test_..."
CLERK_SECRET_KEY="sk_test_..."
NEXT_PUBLIC_CLERK_SIGN_IN_URL="/sign-in"
NEXT_PUBLIC_CLERK_SIGN_UP_URL="/sign-up"

AI_PROVIDER="openrouter"
OPENROUTER_API_KEY="..."
# Or choose groq, cerebras, or gemini and provide its matching key:
# GROQ_API_KEY="..."
# CEREBRAS_API_KEY="..."
# GEMINI_API_KEY="..."

E2B_API_KEY="..."
```

The application reads the provider-specific key selected by `AI_PROVIDER`. Keep all secret values server-side and rotate any token that is accidentally exposed.

### 3. Start PostgreSQL

The included [`docker-compose.yml`](./docker-compose.yml) exposes PostgreSQL on host port `5431`:

```bash
docker compose up -d
```

### 4. Apply the Prisma schema

```bash
npx prisma migrate dev
```

If Prisma Client is not generated after installing dependencies, run:

```bash
npx prisma generate
```

### 5. Start Gen-V

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

The `dev` script also starts Docker Compose automatically, so step 3 can be skipped when Docker is already available and you prefer the single command:

```bash
npm run dev
```

## 🤖 AI provider selection

Set `AI_PROVIDER` to one of the supported values:

| Provider | Value | Key | Notes |
| --- | --- | --- | --- |
| OpenRouter | `openrouter` | `OPENROUTER_API_KEY` | **Recommended.** Configured with tool-capable fallbacks |
| Groq | `groq` | `GROQ_API_KEY` | Works, but free-tier token limits can stop long agent runs |
| Cerebras | `cerebras` | `CEREBRAS_API_KEY` | OpenAI-compatible provider with generous free-tier limits |
| Google Gemini | `gemini` | `GEMINI_API_KEY` | Included as an option, but current agent-kit tool-call replay limitations make it unreliable for this workflow |

OpenRouter’s tool model fallback order is defined in [`src/inngest/model.js`](./src/inngest/model.js). Every configured tool model must support function/tool calling.

## 📦 Available scripts

| Command | Description |
| --- | --- |
| `npm run dev` | Starts PostgreSQL through Docker Compose and launches Next.js in development mode |
| `npm run build` | Creates a production Next.js build |
| `npm run start` | Serves the production build |
| `npm run lint` | Runs the ESLint configuration |
| `npx prisma migrate dev` | Applies local migrations and updates Prisma Client |
| `npx prisma studio` | Opens Prisma Studio for inspecting local data |

Before opening a pull request, run:

```bash
npm run lint
npm run build
```

## ⚠️ Operational notes

- **E2B previews are ephemeral.** A sandbox can expire, so an older preview URL may become unavailable. Generated files and project snapshots are stored separately so users can still browse or export code.
- **Docker is required for the default local database flow.** PostgreSQL is mapped to `localhost:5431`, not the default host port `5432`.
- **Generation consumes credits.** Creating a project or sending a follow-up message calls the usage limiter.
- **GitHub export needs a suitable token.** The UI expects a token with repository creation/content write access, depending on the GitHub token type.
- **The GitHub token is not stored.** It is sent to GitHub for the export request and cleared from the client after success.
- **Do not add generated folders to commits.** `.next/`, `node_modules/`, sandbox output, and local environment files should remain untracked.

## 🔐 Security boundaries

- Clerk middleware protects project routes.
- Server actions verify the current user before reading or mutating projects.
- Fragment export queries are scoped through the owning project and user.
- Project deletion uses both the project ID and authenticated user ID.
- AI provider keys, database credentials, and GitHub tokens must remain in environment variables or request scope.

## 🧪 A quick first run

After signing in:

1. Enter a product idea in the dashboard prompt.
2. Submit it and wait for the agent to create a fragment.
3. Open the project workspace.
4. Select the generated message to load its live preview.
5. Switch to **Code** to inspect the generated file tree.
6. Send a follow-up prompt to iterate.
7. Use **Download** for a ZIP or **GitHub** to publish the selected version.

## 📚 Useful references

- [Next.js App Router documentation](https://nextjs.org/docs/app)
- [Prisma documentation](https://www.prisma.io/docs)
- [Clerk Next.js documentation](https://clerk.com/docs/quickstarts/nextjs)
- [Inngest documentation](https://www.inngest.com/docs)
- [E2B documentation](https://e2b.dev/docs)
- [OpenRouter documentation](https://openrouter.ai/docs)
- [GitHub REST API](https://docs.github.com/en/rest)

## 📄 License

No license file is currently included in the repository. Add an explicit license before distributing Gen-V outside your organization.

<div align="center">

<sub>Built with Next.js, Prisma, Clerk, Inngest, E2B, and a lot of prompts.</sub>

</div>
