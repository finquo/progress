---
name: finquo-docs-maintenance
description: Instructions and guidelines for agents and developers to read, write, and update documentation in the docs folder of this repository.
---

# Docs Maintenance Skill

Guidelines for reading, writing, and updating documentation in this repository.

---

## Core Rules

- Zero bold text formatting: Never use double asterisks for bolding. Use clean, raw markdown with inline code blocks instead.
- Repository-relative links only: Never use absolute paths, local drive letters, or machine-specific usernames. Always link relative to the file location, such as [src/index.ts](../backend/src/index.ts).
- Bullet points over prose: Keep information simple, structured, and easy to parse visually using plain bullet points under headers.
- Inspect git log before updating: When tracking recent changes, run git log or git diff against backend, infra, or frontend to discover new features, schema updates, or refactors.

---

## Documentation Map

- [backend.md](./backend.md)
  - Covers Hono server setup, oRPC router definitions, Better Auth, access control roles, and permission guards.
  - Documents business logic configurations like availability, subscription plans, and quiz schemas.
- [database.md](./database.md)
  - Covers Drizzle ORM schemas, PostgreSQL extensions (`btree_gist`, `pg_trgm`), custom column types (`leaveRange`/`tstzrange`), and database relations.
  - Documents migration commands, custom SQL helpers (`ArrayAppend`), and conflict resolution utilities.
- [infra.md](./infra.md)
  - Covers Terraform / OpenTofu configurations, multi-region AWS providers (`ap-south-1` and `us-east-1`), S3 remote state lockfiles.
  - Documents OpenNext deployment module (`modules/open-terra-next`), serverless SSR Lambdas, CDN routing, and RDS database resources.
- [frontend.md](./frontend.md)
  - Covers Next.js 15 App Router pages, layouts, and role-based route groups.
  - Documents client-side data fetching via oRPC and TanStack Query, Better Auth client setup, and UI components.
- [README.md](./README.md)
  - Stores the core product requirements document (PRD) and feature delivery roadmap.

---

## Workflow for Agents Updating Docs

1. Step 1: Detect changes
   - Check recent commits: `git log -n 10 --oneline`
   - Check file diffs in target folders: `git diff HEAD~1 backend infra`

2. Step 2: Determine impacted documentation files
   - Backend APIs, routers, procedures, auth -> [backend.md](./backend.md)
   - Database tables, columns, indexes, custom types -> [database.md](./database.md)
   - Terraform modules, AWS resources, environment schemas -> [infra.md](./infra.md)
   - Next.js pages, client hooks, UI components -> [frontend.md](./frontend.md)

3. Step 3: Edit documentation
   - Format using simple bullet points.
   - Use inline code formatting for symbols, procedures, table names, and commands.
   - Link all referenced files using repository-relative paths (e.g. `[src/api/routers/admin.ts](../backend/src/api/routers/admin.ts)`).
   - Ensure zero double asterisk bold markers exist in the document.

4. Step 4: Validate
   - Verify that all relative links resolve to actual repository files.
   - Ensure no absolute machine paths were introduced.
