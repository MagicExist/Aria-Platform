# Discovered Rules — Aria Plants

> Etapa 2.2 · Fase Inception · Fecha: 2026-07-10
> Reglas corregibles, orientadas a los agentes. Derivadas de restricciones duras
> expresadas en la entrevista. Al aprobar, se promueven a `project.md`
> (`## Mandated` / `## Forbidden`), acumulándose (append) con sello de fecha.

## Mandated

ALWAYS squash-merge feature branches into `main` (one commit per branch)
ALWAYS run the walking skeleton as a gated, solo Bolt 1 before any other Construction Bolt
ALWAYS write tests alongside code, keep ~80% line coverage, and require CI green before merge
ALWAYS use ESLint + Prettier + TypeScript strict for the app codebase
ALWAYS distribute the app as an Android APK (EAS or local build)

## Forbidden

NEVER provision staging or production server environments for the MVP (backend is managed Supabase; no own server infra)
NEVER expose the Supabase service key in the app client (only the public anon key belongs in the client)
