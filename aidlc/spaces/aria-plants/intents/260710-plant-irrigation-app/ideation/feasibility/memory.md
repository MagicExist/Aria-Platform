<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->
- 2026-07-10T14:50:00Z — Supabase reconcilia el "backend aparte / contrato TBD" de intent-capture: Supabase ES el backend gestionado (Postgres + Auth + API/realtime auto-generada). La app (Expo) habla directo con Supabase vía su cliente JS. La ingesta de lecturas de sensores HACIA Supabase (sensores → Supabase) queda fuera del alcance de este repo; la app solo LEE (polling).
- 2026-07-10T14:50:00Z — Distribución del MVP = Android APK (sideload). Web e iOS se obtienen del mismo código base Expo pero no son el foco de distribución del MVP. iOS además no se verifica localmente (sin Mac).

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->
- 2026-07-10T14:50:00Z — El framework (settings.json, agentes, mcp) asume por defecto stack AWS (Bedrock, CDK, Well-Architected). ESTE proyecto NO usa AWS: usa Supabase (BaaS) + Expo + un hosting simple. La perspectiva aws-platform-agent se reinterpreta como "no se requiere infraestructura AWS para el MVP; backend gestionado (Supabase) + distribución APK". Documento esto para que Application Design / Infrastructure Design no arrastren supuestos AWS.

## Tradeoffs
- 2026-07-10T14:50:00Z — Polling vs realtime para lecturas de sensores: el usuario eligió polling por simplicidad del MVP, aunque Supabase ofrece realtime (WebSocket) sin costo extra. Revisitar si la latencia de alertas se vuelve importante.
- 2026-07-10T20:14:00Z — REVISIÓN EN GATE (rev 1): el usuario redujo el alcance de plataformas a **solo Android** (quita web e iOS). Motivo: iOS no verificable sin Mac; web no requerida. Actualicé intent-statement.md (upstream), feasibility-assessment, constraint-register (TC-1/TC-3/TC-8), raid-log (eliminado R-2 iOS), feasibility-questions (P1/P5) y la regla de project.md. El stack no cambia (Expo/RN+TS+Supabase), solo el target = Android.

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
