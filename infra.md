# Infrastructure & Monorepo

Simple guide to tooling, packages, infrastructure, and deployment.

---

## Shared Packages
- `packages/utils` in [`packages/utils/src/index.ts`](../packages/utils/src/index.ts):
  - `trys(fn)`: Safe synchronous wrapper returning `{ data, error, isSuccess }` in [`packages/utils/src/try.ts`](../packages/utils/src/try.ts)
  - `triedAsync(promise, tag)`: Safe asynchronous wrapper returning `{ data, error, isSuccess }` in [`packages/utils/src/try.ts`](../packages/utils/src/try.ts)
  - `tryAsync(promise, tag)`: Tuple-style async wrapper returning `[error, data]` in [`packages/utils/src/try.ts`](../packages/utils/src/try.ts)
- `packages/example` in [`packages/example/package.json`](../packages/example/package.json):
  - Minimal package boilerplate for adding new monorepo utilities

---

## Cloud Infrastructure (Terraform / OpenTofu)

### Terraform Setup & Providers
- Directory: [`infra/`](../infra)
- Main Configuration: [`main.tf`](../infra/main.tf)
- Engine Lockfile: [`.terraform.lock.hcl`](../infra/.terraform.lock.hcl)
- Remote State: S3 bucket (`finquo-app-opentofu-state`) with `use_lockfile = true`
- AWS Providers:
  - `aws.main` in region `ap-south-1` (primary infrastructure)
  - `aws.global` in region `us-east-1` (CloudFront distributions, certificates, global edge services)

### Next.js Deployment ([`next.tf`](../infra/next.tf))
- Module: [`modules/open-terra-next/`](../infra/modules/open-terra-next) (forked from `open-terra-next` / terranext)
  - Root Module: [`modules/open-terra-next/main.tf`](../infra/modules/open-terra-next/main.tf)
  - Module Variables: [`modules/open-terra-next/variables.tf`](../infra/modules/open-terra-next/variables.tf)
- Engine: OpenNext build artifact reader (`opennext_build_path = "../frontend/.open-next"`)
- Submodules:
  - S3 Bucket: [`modules/open-terra-next/modules/assets/main.tf`](../infra/modules/open-terra-next/modules/assets/main.tf) (hosts static assets and Next.js client bundles)
  - CloudFront CDN: [`modules/open-terra-next/modules/cdn/main.tf`](../infra/modules/open-terra-next/modules/cdn/main.tf) (edge routing, viewer host headers, and Route53 DNS)
  - Lambda Server: [`modules/open-terra-next/modules/lambda/main.tf`](../infra/modules/open-terra-next/modules/lambda/main.tf) (serverless SSR handler for Next.js dynamic routes)
  - Revalidation Queue: [`modules/open-terra-next/modules/revalidation-queue/main.tf`](../infra/modules/open-terra-next/modules/revalidation-queue/main.tf) (SQS queue with KMS encryption for ISR)
  - Revalidation Seeder: [`modules/open-terra-next/modules/revalidation-seeder/main.tf`](../infra/modules/open-terra-next/modules/revalidation-seeder/main.tf) (pre-seeds static page revalidation events)
- Outputs:
  - `domain`: Public CloudFront HTTPS distribution URL

### Database Infrastructure ([`test/db.tf`](../infra/test/db.tf))
- Service: AWS RDS PostgreSQL 16
- Instance: `db.t4g.small` (General Purpose ARM)
- Storage: 20 GB baseline, auto-scaling up to 100 GB GP3 (encrypted at rest)
- Subnet Group: Custom multi-AZ DB subnet group (`finquo-app-postgres-subnet-group`)
- Outputs:
  - `database_url`: Formatted connection URI string

---

## Environment Configuration (Varlock)
- Schemas defined in [`.env.schema`](../.env.schema)
- Workspace load paths defined in [`backend/package.json`](../backend/package.json)
- Generate TypeScript env bindings: `bun run env:generate`
- Core variables:
  - `DATABASE_URL`: PostgreSQL connection string
  - `BETTER_AUTH_SECRET`: Encryption key for sessions and tokens
  - `BETTER_AUTH_URL`: Canonical backend URL (e.g. `http://localhost:3000`)
  - `CORS_ORIGIN`: Allowed frontend origin (e.g. `http://localhost:3001`)
  - `NEXT_PUBLIC_SERVER_URL`: Backend URL for client-side queries

---

## Monorepo Commands ([`package.json`](../package.json))
- `bun run dev` - Start all workspaces in parallel development mode
- `bun run build` - Build all packages and applications
- `bun run check-types` - Validate TypeScript types across all projects
- `bun run check` - Run Biome linter and formatter with auto-fix
- `bun run format` - Format all source files with Biome
- `bun run frontend` - Run commands targeted to the frontend
- `bun run backend` - Run commands targeted to the backend (or `bun run --hot src/index.ts --port 3000`)
- `bun run env:generate` - Re-generate Varlock type-safe environment accessors
