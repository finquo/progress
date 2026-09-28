# Finquo App Documentation

A quick and simple guide to the entire monorepo.

---

## Architecture Overview
- **Type**: Monorepo managed with Bun and Vite+ (`vite-plus`)
- **Backend**: Hono web server + oRPC type-safe RPC + Better Auth + Drizzle ORM
- **Frontend**: Next.js 15 (App Router) + Tailwind CSS + shadcn/ui + TanStack Query
- **Database**: PostgreSQL 16 (AWS RDS via Terraform / Drizzle migrations)
- **Shared Packages**: `packages/utils` (safe error handling), `packages/example`
- **Environment**: Varlock type-safe environment schemas (`.env.schema`)

---

## Project Structure
- [`backend`](file:///C:/Users/rajat/think-forge/app/backend) - API routes, auth logic, Drizzle schemas, database queries
- [`frontend`](file:///C:/Users/rajat/think-forge/app/frontend) - User interface, Next.js routes, shadcn UI components, RPC client
- [`packages/utils`](file:///C:/Users/rajat/think-forge/app/packages/utils) - Shared async/sync error handling helpers (`triedAsync`, `trys`)
- [`packages/example`](file:///C:/Users/rajat/think-forge/app/packages/example) - Example workspace package
- [`infra`](file:///C:/Users/rajat/think-forge/app/infra) - Terraform scripts for AWS RDS PostgreSQL
- [`docs`](file:///C:/Users/rajat/think-forge/app/docs) - Project documentation

---

## Detailed Documentation
- [Backend Documentation](file:///C:/Users/rajat/think-forge/app/docs/backend.md) - APIs, authentication, permissions, routers
- [Frontend Documentation](file:///C:/Users/rajat/think-forge/app/docs/frontend.md) - Pages, routes, components, client state
- [Database Documentation](file:///C:/Users/rajat/think-forge/app/docs/database.md) - Schema tables, models, relations, constraints
- [Infrastructure & Setup](file:///C:/Users/rajat/think-forge/app/docs/infra.md) - Scripts, commands, Terraform, environment variables

---

## Quick Start
- Install dependencies: `bun install`
- Run all apps in dev mode: `bun run dev`
- Run frontend only: `bun run frontend dev`
- Run backend only: `bun run backend dev`
- Generate env types: `bun run env:generate`
- Run type checking: `bun run check-types`
- Format & lint: `bun run check`
