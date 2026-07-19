# Delivery Planning — Stage Diary

> Observation log maintained by the orchestrator while running this stage.
> Four standard headings. ISO 8601 timestamps. Read before the approval gate
> for the §13 learnings ritual.

## Interpretations

- 2026-07-19T17:15:00Z — El default del scope mvp es "todos los Bolts los ejecuta aidlc-developer-agent (AI)" porque 1.5 (team-formation) está SKIP. PERO `team.md` afirma un equipo REAL de 2 personas (senior+junior) con reparto explícito. Se honra la práctica afirmada: la asignación de Bolts sigue el reparto senior/junior de `team.md`, no el default AI.
- 2026-07-19T17:15:00Z — Walking-skeleton-first está MANDADO (org/team/project "ALWAYS run walking skeleton as gated, solo Bolt 1"). Bolt 1 = walking skeleton (login→crear zona→crear planta→asignar sensor→ver humedad), solo (senior) y con gate.
- 2026-07-19T17:15:00Z — El walking skeleton cruza varias unidades finamente (foundation+auth+zones+plants+sensors+dashboard), así que su granularidad es "rebanada fina que cruza unidades"; los Bolts siguientes completan por unidad/bundle.

## Deviations

- 2026-07-19T17:20:00Z — Se sobreescribe el default del scope mvp ("todos los Bolts = AI developer") con el reparto senior/junior de `team.md` (práctica afirmada): senior toma WS + Bolts pesados/integración; junior toma Bolts ligeros gated. La práctica afirmada gana sobre el default del scope.

## Tradeoffs

- 2026-07-19T17:20:00Z — Iteración de Construcción = **unit-major** (no el default stage-major): plan walking-skeleton-first / Bolt-based con coherencia de diseño por unidad. Los 4 gates de diseño siguen firing, pero en cascada al final del bloque de diseño.
- 2026-07-19T17:20:00Z — El walking skeleton (Bolt 1) es una rebanada fina que cruza foundation+auth+zones+plants+sensors+dashboard (no una unidad sola), porque las unidades son por-feature y el skeleton necesita un camino vertical mínimo. Los Bolts siguientes completan por unidad/bundle.

## Open questions
