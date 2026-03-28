# Workspace

## Overview

CipherScore — a digital identity risk intelligence platform. Users enter their email to get a full risk profile including breach exposure, OAuth app risk, session age, MFA status, and shadow AI usage.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **Build**: esbuild (CJS bundle)
- **Frontend**: React + Vite, Tailwind CSS, shadcn/ui, Framer Motion, Recharts

## Structure

```text
artifacts-monorepo/
├── artifacts/              # Deployable applications
│   ├── api-server/         # Express API server
│   └── cipherscore/        # React + Vite frontend (SaaS dashboard)
├── lib/                    # Shared libraries
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   └── db/                 # Drizzle ORM schema + DB connection
├── scripts/                # Utility scripts
│   └── src/                # Individual .ts scripts
│       └── seedCipherScore.ts  # Seeds historical scan data
├── pnpm-workspace.yaml
├── tsconfig.base.json
├── tsconfig.json
└── package.json
```

## CipherScore Features

- **Risk Score Engine**: Weighted scoring across 5 factors (breach exposure, OAuth risk, session age, MFA status, shadow AI)
- **Risk Categories**: Low (0–33), Medium (34–66), High (67–84), Critical (85–100)
- **Visualization**: Bar chart (risk vectors), Line chart (score history over time)
- **Action Engine**: Actionable security fixes that dynamically update the score
- **Insights Panel**: Human-readable explanations of risk drivers
- **Scan History**: Stored in PostgreSQL, displayed on landing page and dashboard
- **Dark/Light mode**: Theme toggle in the top header

## API Routes

- `GET /api/healthz` — health check
- `POST /api/scans` — run identity scan for an email
- `GET /api/scans/history?email=&limit=` — get scan history
- `POST /api/actions/apply` — apply a security fix and recalculate score

## Database Schema

- `scans` table: id, email, riskScore, riskCategory, breachCount, factors (jsonb), actions (jsonb), insights (jsonb), scannedAt

## TypeScript & Composite Projects

Every package extends `tsconfig.base.json` which sets `composite: true`. Running codegen: `pnpm --filter @workspace/api-spec run codegen`

## Root Scripts

- `pnpm run build` — typecheck + build all packages
- `pnpm run typecheck` — full typecheck
- `pnpm --filter @workspace/scripts run seedCipherScore` — seed historical scan data
