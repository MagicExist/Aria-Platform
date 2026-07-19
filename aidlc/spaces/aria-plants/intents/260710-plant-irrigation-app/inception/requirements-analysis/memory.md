<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->
- 2026-07-12T02:19:51Z — P1 excluye "notas/descripción libre" (no eligió D); la planta modela solo nombre (obligatorio), especie/tipo (opcional), foto (opcional) y fecha de registro (automática).
- 2026-07-12T02:19:51Z — P4=A sin marcador de "sin dato": el MVP muestra la última lectura sin lógica de staleness; se documenta como mejora post-MVP en Out of Scope.

## Deviations
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->
- 2026-07-12T02:19:51Z — ninguna; el stage siguió la prosa. Se omitió la edición directa del state file (Step 11): la advancement la hace `report` del engine, no el prosa.

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->
- 2026-07-12T02:19:51Z — umbral solo-mínimo (P3=A) en vez de rango min–max: más simple para el MVP de humedad; el rango queda como extensión futura.
- 2026-07-12T02:19:51Z — auth email+password (P5=A) sin OAuth/verificación: menor fricción de integración para el walking skeleton (Bolt 1).

## Open questions
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
- 2026-07-12T02:19:51Z — ¿la app incluye capa de simulación/ingreso de lecturas hasta que exista el contrato de sensores? Diferido a Application Design (OQ-1).
- 2026-07-12T02:19:51Z — almacenamiento de la foto (Supabase Storage vs. URL) y efecto de borrar planta sobre sensores/lecturas: a decidir en Application Design (OQ-2/OQ-3).
