# Infrastructure & Monorepo

Simple guide to tooling, packages, infrastructure, and deployment.

---

## Shared Packages
- **`packages/utils`**:
  - `trys(fn)`: Safe synchronous wrapper returning `{ data, error, isSuccess }`
  - `triedAsync(promise, tag)`: Safe asynchronous wrapper returning `{ data, error, isSuccess }`
  - `tryAsync(promise, tag)`: Tuple-style async wrapper returning `[error, data]`
- **`packages/example`**:
  - Minimal package boilerplate for adding new monorepo utilities

---

## Cloud Infrastructure (Terraform)
- **Tool**: Terraform / OpenTofu (`infra/`)
- **Cloud Provider**: AWS (`ap-south-1` region)
- **State Storage**: AWS S3 bucket (`finquo-app-opentofu-state`)
- **Database Resource**:
  - Service: AWS RDS PostgreSQL 16
  - Instance: `db.t4g.small`
  - Storage: 20 GB baseline (auto-scaling up to 100 GB GP3)
  - DB Name: `finquo-app`

---

## Environment Configuration (Varlock)
- Schemas defined in `.env.schema`
- Generate TypeScript env bindings: `bun run env:generate`
- Core variables:
  - `DATABASE_URL`: PostgreSQL connection string
  - `BETTER_AUTH_SECRET`: Encryption key for sessions and tokens
  - `BETTER_AUTH_URL`: Canonical backend URL (e.g. `http://localhost:3000`)
  - `CORS_ORIGIN`: Allowed frontend origin (e.g. `http://localhost:3001`)
  - `NEXT_PUBLIC_SERVER_URL`: Backend URL for client-side queries

---

## Monorepo Commands
- `bun run dev` - Start all workspaces in parallel development mode
- `bun run build` - Build all packages and applications
- `bun run check-types` - Validate TypeScript types across all projects
- `bun run check` - Run Biome linter and formatter with auto-fix
- `bun run format` - Format all source files with Biome
- `bun run frontend` - Run commands targeted to the frontend
- `bun run backend` - Run commands targeted to the backend
- `bun run env:generate` - Re-generate Varlock type-safe environment accessors
