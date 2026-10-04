# GeM

GeM is a campus issue-management app for students, facilities staff, and administrators. Students submit and follow reports; administrators prioritize, group, and assign them; staff post updates and complete work; students confirm the resolution or reopen the issue.

## Features

- Clerk sign-in with GeM profiles and role-specific workspaces.
- PostgreSQL-backed profiles, categories, campus locations, issues, comments, status history, duplicate reports, and notifications.
- Issue reporting with campus location, affected-person count, photo upload, automatic priority scoring, and duplicate review.
- Admin queue for searching, filtering, prioritizing, assigning, and grouping reports.
- Staff work queue for assigned issues and progress updates.
- Student issue tracking, notifications, and resolution confirmation or reopening.
- Database-driven analytics, recurring-location/category insights, and seeded demo records.
- Responsive student, staff, and admin screens.

## Development

The workspace uses pnpm. The existing Replit workflows start the API and web preview. The API is served under `/api`; the web app uses the shared proxy.

After changing database tables, apply the development schema with:

```sh
pnpm --filter @workspace/db run push
```

The API seeds demo categories, locations, users, and 26 issues when it starts in development. Seed records are created idempotently and are not added automatically in production.

For API contract changes, update `lib/api-spec/openapi.yaml` and regenerate the typed client and schemas:

```sh
pnpm --filter @workspace/api-spec run codegen
```

The development profile page includes a role selector so one authenticated account can demonstrate all three workspaces. That selector is compiled out of the production web build. In production, set `CAMPUSFIX_ADMIN_EMAILS` and `CAMPUSFIX_STAFF_EMAILS` to comma-separated, verified campus addresses that should receive those roles. The API always derives email and identity from Clerk.

## Photo storage note

GeM requests a short-lived signed upload URL and keeps image bytes out of PostgreSQL. Replit App Storage could not be provisioned in this workspace, so the current development implementation stores uploaded images in `.data/campusfix-uploads` and serves them through an authenticated API route. This fallback is suitable for previewing the upload flow, but local files are not a durable production object store. Configure persistent object storage before relying on uploaded photos in a published deployment.

## Main workspace paths

- `artifacts/campusfix` — responsive React/Vite application.
- `artifacts/api-server` — Clerk-protected Express API and development seed data.
- `lib/api-spec` — OpenAPI contract and generated React Query/Zod clients.
- `lib/db` — PostgreSQL connection and Drizzle schema.