# DealFlow360

Sales-quotation workspace: Express + Prisma API (`backend`), React + Vite portal (`frontend`), shared types (`shared`).

## Prerequisites

- Windows Node.js (v22, `node.exe`) + npm.
- Docker (Postgres) or a local Postgres 16.
- Ports free: `4000` (API), `5173` (web), `5433` (DB).

## Setup

```sh
npm install
npx prisma generate --schema=backend/prisma/schema.prisma
docker compose up -d        # Postgres on localhost:5433
```

Copy and fill env files (both are committed as `.env.example`):

- `backend/.env` — `DATABASE_URL`, `JWT_SECRET`, `PORTAL_TOKEN_SECRET`, `PORT`, `CORS_ORIGIN`
- `frontend/.env` — `VITE_API_URL` (e.g. `http://localhost:4000`)

```sh
cd backend && npx prisma migrate deploy
npm run seed -w backend     # idempotent demo dataset
```

## Run

```sh
npm run dev                 # backend (:4000) + frontend (:5173)
npm run typecheck           # shared + backend + frontend
npm run test -w backend     # vitest
npm run build               # shared -> backend -> frontend
```

Seeded logins (password `dealflow360`): `rep@dealflow360.test`, `manager@dealflow360.test`, `finance@dealflow360.test`, `admin@dealflow360.test`.

## Notes

- `backend/tests/` holds `*.test.ts` scaffolds; only files listed in `backend/vitest.config.ts` `include` actually run. Empty scaffolds stay unlisted until filled in.
- Regenerate the Prisma client on each OS you run on (`prisma generate`) — the checked-in `node_modules` client is platform-specific.
