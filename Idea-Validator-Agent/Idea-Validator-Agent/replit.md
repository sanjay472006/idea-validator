# Idea Validator Micro-Agent

## Overview

An AI-powered full-stack web application that helps entrepreneurs, students, and innovators validate their startup or project ideas before investing time and resources. Users submit ideas and receive structured AI evaluation reports.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **Frontend**: React + Vite (artifacts/web), deployed at `/`
- **Backend**: Express 5 (artifacts/api-server), deployed at `/api`
- **AI**: OpenAI via Replit AI Integrations (gpt-5.2, no user API key needed)
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (zod/v4), drizzle-zod
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)

## Structure

```text
artifacts-monorepo/
├── artifacts/
│   ├── api-server/        # Express API server (routes, DB, AI)
│   └── web/               # React + Vite frontend
├── lib/
│   ├── api-spec/          # OpenAPI spec + Orval codegen config
│   ├── api-client-react/  # Generated React Query hooks
│   ├── api-zod/           # Generated Zod schemas from OpenAPI
│   ├── db/                # Drizzle ORM schema + DB connection
│   └── integrations-openai-ai-server/  # OpenAI client wrapper
├── scripts/
└── pnpm-workspace.yaml
```

## Key Features

- Idea submission form (title, description, problem statement, target users)
- AI analysis using GPT-5.2 via Replit AI Integrations
- Structured validation report: summary, market demand, competitors, strengths, weaknesses, suggestions
- Scoring system: innovation, feasibility, scalability, competition (0–10 scale)
- Idea history dashboard with status tracking (pending → validating → completed/failed)
- Re-validation support
- Polling for real-time status updates

## Database Schema

### `ideas` table
- `id` (serial, PK)
- `title` (text)
- `description` (text)
- `problem_statement` (text)
- `target_users` (text)
- `status` (text: pending | validating | completed | failed)
- `report` (jsonb — stores the full ValidationReport)
- `created_at`, `updated_at` (timestamps)

## API Endpoints

- `GET /api/healthz` — health check
- `GET /api/ideas` — list all ideas
- `POST /api/ideas` — submit a new idea (triggers async AI validation)
- `GET /api/ideas/:id` — get idea with full validation report
- `DELETE /api/ideas/:id` — delete an idea
- `POST /api/ideas/:id/validate` — re-run AI validation

## AI Integration

Uses Replit AI Integrations proxy for OpenAI. No user API key required — charges are billed to Replit credits. The integration is set up in `lib/integrations-openai-ai-server/`.

## Running Codegen

After modifying `lib/api-spec/openapi.yaml`:

```bash
pnpm --filter @workspace/api-spec run codegen
```

## Database Migrations

```bash
pnpm --filter @workspace/db run push
```
