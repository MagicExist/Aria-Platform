<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->
- 2026-07-10T20:30:00Z — CAMBIO DE ALCANCE: el MVP muestra SOLO la variable de HUMEDAD; la LUZ queda fuera del MVP (aunque intent-capture mencionaba humedad+luz). El modelo de datos debe permitir múltiples tipos de variable, pero el MVP surface solo humedad.
- 2026-07-10T20:30:00Z — "Ver variables" no es pantalla aparte: se muestran dentro del Dashboard general / vista de planta. No crear una pantalla dedicada de variables en el MVP.
- 2026-07-10T20:30:00Z — Walking skeleton confirmado: Login → registrar 1 planta → asignar 1 sensor → ver su humedad. Esta es la primera rebanada e2e (informa delivery-planning/Bolt 1).

## Deviations

## Tradeoffs

## Open questions
- 2026-07-10T20:30:00Z — P4 (ingreso/simulación de lecturas en la app) DIFERIDO a Application Design: el front y el backend se desarrollan en paralelo, así que el origen de datos se decide después. El diseño debe aislar el acceso a datos (capa/adaptador) para no atarse ahora. Confirmar en Application Design.
- 2026-07-10T20:30:00Z — Alertas = in-app + push (P3). Push (Expo Notifications) añade algo de complejidad; validar viabilidad exacta en diseño. Umbrales FIJOS en MVP (personalizados fuera).
- 2026-07-10T20:30:00Z — Histórico/tendencias: no es must-have ni se excluyó explícitamente → "Could" no priorizado. Confirmar si entra si sobra tiempo.

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
