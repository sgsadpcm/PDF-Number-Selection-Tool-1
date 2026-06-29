# Workspace

## Overview

pnpm workspace monorepo using TypeScript. Each package manages its own dependencies.

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

## Key Commands

- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- `pnpm --filter @workspace/api-server run dev` — run API server locally

See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details.

## Artifacts

### inspection-schedule (web)
Chinese-language form tool「機電消防公設檢驗時間表產生器」. Lets the user enter
project name, region (北部 / 中南部), unpublished flag, total days, and per-day
group counts (機電 / 電梯 / 消防) plus per-group morning/afternoon checklists,
extra inspection personnel, notes and notices. Exports a Word document.

- Frontend: `artifacts/inspection-schedule/src/pages/inspection-form.tsx`
- Frontend state/labels/serialization: `artifacts/inspection-schedule/src/lib/inspection-state.ts`
- Frontend content list (mirror of backend): `artifacts/inspection-schedule/src/lib/inspection-content.ts`
- Backend route: `POST /api/inspection/generate` → returns `.docx` blob.
  See `artifacts/api-server/src/routes/inspection.ts`.
- Backend content + people mapping: `artifacts/api-server/src/lib/inspection/data.ts`
- Word generation: built **programmatically per request** with the `docx`
  package in `artifacts/api-server/src/lib/inspection/generator.ts`. No
  template file is used. The date column and 上午 / 下午 column use proper
  `verticalMerge: "restart" / "continue"` so cells are visually merged.
- Cross-day group numbering (機電1., 機電2., ... continues across days) is
  computed on the frontend and serialized into the request payload.
- Auto-mapping inspection items → 配合檢測人員 by `shortKey` happens on the
  backend; extra people from the form are appended and deduped preserving order.
- Font: 微軟正黑體 10pt bold throughout.
