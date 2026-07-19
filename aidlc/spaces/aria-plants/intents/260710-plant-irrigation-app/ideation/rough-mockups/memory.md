<!-- INVARIANT: examples are single-line HTML comments so a fresh template parses to total=0 (MEMORY_EMPTY). Do NOT un-comment or split across lines. t100 guards this. -->
> This file is maintained by the orchestrator during stage execution. Add observations at the gate ritual, not by editing here directly.

## Interpretations
<!-- example: 2026-05-29T10:14:32Z — chose REST over GraphQL; the consuming team only needs CRUD, revisit if subscriptions land -->
- 2026-07-10T20:45:00Z — No hay pantalla/tab dedicada de Alertas en el MVP: las alertas se muestran dentro del Dashboard (sección "alertas recientes") + notificación push. Tabs inferiores = Dashboard · Plantas · Zonas · Perfil (4 tabs).
- 2026-07-10T20:45:00Z — Tema visual: dark-mode como principal (dark-first), estilo minimalista, marca "Aria Plants". No se pidió tema verde explícito; verde solo como acento sutil opcional. Modo claro es opcional/posterior.
- 2026-07-10T20:45:00Z — i18n desde el inicio (español + inglés con selector). Los wireframes usan etiquetas en español como idioma por defecto de trabajo.

## Deviations
- 2026-07-10T21:00:00Z — Reviewer (product-lead) iteración 1 = NOT-READY con 4 hallazgos. Correcciones aplicadas: (1) añadido wireframe "Crear/editar zona" (paridad con Plantas); (2) bootstrap de primera zona vía "crear zona" inline en el form de planta; (3) resuelta contradicción de tema → oscuro-only en MVP (claro post-MVP), quitado el toggle Oscuro/Claro activo; (4) quitado "Olvidé clave" del login (diferido). Menores: quitado buscador de Plantas; umbral marcado como fijo/informativo.
<!-- example: 2026-05-29T10:14:32Z — skipped the optional caching layer the stage prose suggested; the dataset is small enough that it adds risk -->

## Tradeoffs
<!-- example: 2026-05-29T10:14:32Z — picked TDD over BDD this run; the team is unit-first and the domain is well-understood -->

## Open questions
- 2026-07-10T21:05:00Z — Reviewer iteración 2 = READY. Sugerencia no-bloqueante pendiente: confirmar la cardinalidad "una planta pertenece a UNA sola zona" (asumida en los flujos) explícitamente con el usuario en refined-mockups o requirements-analysis, por si se quiere permitir planta en varias zonas.
<!-- example: 2026-05-29T10:14:32Z — confirm the retention window with compliance before the next stage hardens the schema -->
