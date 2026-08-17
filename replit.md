# SmartRoom Hub

SmartRoom Hub is a responsive IoT monitoring and control dashboard for an ESP32-S3 room monitor, with authenticated device communication and a simulator mode for testing without hardware.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `artifacts/smartroom-hub/src/App.tsx` — connected dashboard UI and page routing.
- `artifacts/smartroom-hub/src/index.css` — SmartRoom Hub dark command-center theme.
- `artifacts/api-server/src/routes/smartroom.ts` — dashboard endpoints and authenticated ESP32 device endpoint.
- `lib/api-spec/openapi.yaml` — source of truth for generated API clients and validation schemas.

## Architecture decisions

- Dashboard data is served through the shared API server, so the browser never receives the device API token.
- Demo Mode produces moving telemetry in the server and is the default so the full interface is usable without hardware.
- Real Device Mode only reports the ESP32 as online after authenticated telemetry arrives within the configured offline timeout.
- Device commands are desired state on the server; the ESP32 receives them on its authenticated sync poll and confirms actual state via telemetry.

## Product

- Overview dashboard with environmental readings, device status, quick controls, and recent events.
- Room Controls for light, fan, fan speed, and alarm override.
- Sensors, History, Events, Device, and Settings screens with responsive mobile navigation.
- ESP32-S3 `POST /api/device` endpoint supporting authenticated telemetry and sync actions.

## User preferences

- Recreate the supplied SmartRoom Hub screenshots as one connected application, not unrelated pages.
- Preserve Demo Mode alongside Real Device Mode and never claim hardware is online without authenticated telemetry.

## Gotchas

- Keep `lib/api-spec/openapi.yaml` and generated clients in sync by running the API codegen command after contract changes.
- The API uses `DEVICE_API_TOKEN` when present and falls back to the existing server-only `SESSION_SECRET` for local wiring; never put either value in frontend code.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
