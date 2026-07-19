# User Stories — Stage Diary

> Observation log maintained by the orchestrator while running this stage.
> Four standard headings. ISO 8601 timestamps. Read before the approval gate
> for the §13 learnings ritual.

## Interpretations

- 2026-07-14T16:41:02Z — Resumen de resume; la stage estaba parqueada en `[-]` sin artefactos en disco (el dir `user-stories/` no existía), así que se ejecuta como corrida limpia, no como reanudación de trabajo parcial.
- 2026-07-14T16:41:02Z — Las **personas** de esta stage son los USUARIOS de la app (cuidador de plantas), no el equipo de desarrollo. El desnivel senior/junior de `team.md` es input para delivery-planning (2.8), no para las personas de usuario.

## Deviations

- 2026-07-14T16:41:02Z — Acceptance criteria se escriben en formato **Given/When/Then (BDD)** por la guardrail de fase inception (`phases/inception.md` → User Stories), aunque `requirements.md` usó prosa "Aceptación:". Se mantiene trazabilidad RF→historia.

- 2026-07-19T15:05:00Z — Reanudación (sesión 2026-07-19): las 6 preguntas ya estaban respondidas por el usuario en modo self-guided; faltaban generación de `personas.md` y `stories.md`. Se confirmó el set de respuestas con el usuario antes de generar ("Sí, generar").
- 2026-07-19T15:05:00Z — **Q5 → regla "zona-primero"**: el usuario define que la foto es Must como feature pero opcional al crear una planta, y que crear una zona es OBLIGATORIO antes de poder crear plantas (no hay zona por defecto implícita). Interpretación confirmada: la app fuerza que exista ≥1 zona; "crear zona" es dependencia dura de "crear planta". Esto da forma al onboarding y a las dependencias de historias (HU zona → HU planta).

## Tradeoffs

- 2026-07-19T15:05:00Z — Q3=A (una historia por operación CRUD) genera más historias pero maximiza trazabilidad/testabilidad INVEST; se aceptó el mayor recuento a cambio de granularidad fina que alimenta mejor Delivery Planning y los criterios de aceptación BDD.
- 2026-07-19T15:05:00Z — Sensores: `requirements.md` (RF-C) solo define registrar/asignar (RF-C1) y ver (RF-C2); no lista editar/eliminar sensor. Se respeta el alcance: no se inventan historias CRUD de sensor más allá de RF-C. Editar/desasignar sensor se marca como Could con nota, sin acceptance detallado.

## Open questions

- 2026-07-14T16:41:02Z — `team-practices.md` (2026-07-10) dice "proyecto personal en solitario"; `team.md` (afirmado 2026-07-11) dice "equipo de dos personas (senior + junior)". La fuente más reciente (team.md) gana. Afecta delivery-planning, no las personas de usuario aquí.
- 2026-07-19T15:10:00Z — Reviewer (aidlc-product-lead-agent) verdict READY. Hallazgo carried-forward: los criterios de aceptación de HU-D4 (eliminar zona con plantas) y HU-B6 (eliminar planta con sensores) son disyuntivos por OQ-3 (cascada vs desasignar, sin resolver en requirements). Application Design DEBE fijar la regla de cascada antes de que Build & Test escriba pruebas concretas para esas dos historias. Delivery Planning debe secuenciar esa decisión temprano.
- 2026-07-19T15:10:00Z — Señal para Delivery Planning (2.8): el walking skeleton de las prácticas ("login → registrar planta → asignar sensor → ver humedad") NO incluía "crear zona"; la regla zona-primero (Q5) lo convierte en paso obligatorio previo, así que el Bolt 1 debe incluir HU-D1.
