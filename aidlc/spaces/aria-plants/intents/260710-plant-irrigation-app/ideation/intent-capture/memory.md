<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->
- 2026-07-10T14:30:00Z — El "health/salud de la planta" que aparece en la descripción original queda EXPLÍCITAMENTE fuera del MVP (P3, nota del usuario). El MVP muestra las variables crudas de sensores (humedad, luz) + centralización + alertas + zonas; el indicador derivado de "salud" es post-MVP. Lo trato como límite de alcance en intent-statement.
- 2026-07-10T14:30:00Z — Proyecto personal, motivación de aprendizaje/exploración (multiplataforma + IoT), escala inicial pequeña (1 usuario, 11 plantas, 1 zona) pero con intención de escalar. A pesar del perfil "personal/PoC", el usuario eligió deliberadamente alcance MVP y pide auth completa (registro, autenticación, autorización) — coherente con el objetivo de aprendizaje y de escalar; no lo trato como contradicción.

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
- 2026-07-10T14:30:00Z — Frontera de datos (P6=B): backend construido aparte, este repo es SOLO la app; el contrato de la API de sensores se define después. Feasibility/Application Design deben tratar el backend como dependencia externa con contrato por definir (asumir contrato/mock hasta que exista).
- 2026-07-10T14:30:00Z — iOS: multiplataforma web/Android/iOS, pero iOS no se podrá testear (sin Mac). Confirmar en feasibility la estrategia (p.ej. framework cross-platform tipo React Native/Flutter/Expo) y que iOS quede como "build no verificado localmente".
