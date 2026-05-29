# Snap Or Slap Backend

This document reflects the current backend repository status after cleanup.

## Runtime

- Framework: Next.js App Router API routes.
- Language: TypeScript.
- Database: PostgreSQL through `pg`.
- Deployment: Railway, configured by `railway.toml`.
- API docs: OpenAPI JSON at `/api/docs`, Swagger UI at `/api/docs/ui`.
- Health check: `/api/health`.

Production base URL:

```text
https://backend-production-2dda7.up.railway.app
```

## Current API Scope

The backend currently supports these route groups:

- Auth: register, login, refresh, signout, username check, change password.
- Users and profile: current user, public profile preview, settings, stats, activities, badge checks.
- Friends: list friends, send request, pending requests, respond to requests, unfriend.
- Challenges: create/list/detail/update/delete, public browse, formation join/decline/ready/leave/invite, active check-ins, stats, cancel, history, recreate, milestone check, nudges.
- Notifications: list, mark read, delete.
- Widget and sync: widget summary and pending overlay sync.
- Cron jobs: formation transition and heart deduction through `/api/crons/[jobName]`.
- Docs and diagnostics: `/api/docs`, `/api/docs/ui`, `/api/test-data`.

Removed from backend source:

- Claude agent instructions.
- Sprint planning markdown files.
- Pages Router error pages.
- Placeholder `hello`, `teams`, `teams/[id]/slap`, and `challenges/[id]/proof` endpoints.
- `tsconfig.migrate.json`, because migration now uses `scripts/migrate.js`.

## Local Commands

Install dependencies:

```bash
npm ci
```

Run development server:

```bash
npm run dev
```

Run quality checks:

```bash
npm run typecheck
npm run test:coverage:ci
```

Run database migrations:

```bash
npm run db:migrate
```

Build production artifact:

```bash
npm run build
```

## Environment

Required environment variables:

```text
DATABASE_URL
JWT_ACCESS_SECRET
JWT_REFRESH_SECRET
CRON_SECRET
APP_BASE_URL
NODE_ENV
```

For Railway internal PostgreSQL URLs, SSL is disabled automatically. For public PostgreSQL URLs, SSL uses `rejectUnauthorized: false`.

## CI And SonarCloud

Backend quality is checked by `.github/workflows/backend-sonar.yml`:

1. `npm ci`
2. `npm run typecheck`
3. `npm run test:coverage:ci`
4. Verify `coverage/lcov.info`
5. SonarCloud scan

SonarCloud settings live in `sonar-project.properties`.

## Notes

- Most application endpoints still accept `user_id` as a query parameter for the current mobile/backend contract.
- Check-in proof upload is handled by `POST /api/challenges/[id]/checkins`, not by a separate `/proof` endpoint.
- `scripts/copy-standalone-assets.cjs` is still required by the production build because the app deploys with Next.js standalone output.
