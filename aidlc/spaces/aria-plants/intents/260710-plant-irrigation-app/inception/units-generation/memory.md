# Units Generation — Stage Diary

> Observation log maintained by the orchestrator while running this stage.
> Four standard headings. ISO 8601 timestamps. Read before the approval gate
> for the §13 learnings ritual.

## Interpretations

- 2026-07-19T16:35:00Z — 2.7 define SOLO la topología (qué depende de qué); el orden de construcción y el walking skeleton (login→crear zona→crear planta→asignar sensor→ver humedad) los decide Delivery Planning (2.8). No se recomienda orden ni ruta crítica aquí.
- 2026-07-19T16:35:00Z — Despliegue: la app es un único APK Android; todas las unidades se integran en una sola app (embedded), no hay despliegue independiente por unidad. La "unidad" es límite de trabajo/Bolt, no de despliegue.

## Deviations

- 2026-07-19T16:55:00Z — Reviewer 1 (aidlc-architecture-reviewer-agent) NOT-READY (1 HIGH, 2 MEDIUM, 3 LOW). HIGH: contradicción de propiedad del fetch de lecturas entre `sensors` (necesita última lectura por sensor, HU-C2) y `dashboard` (dueño de ReadingsSource); añadir la arista inversa haría ciclo. Fix (alineado con application-design, donde ReadingsSource vive en lib/readings compartido): mover el adaptador `ReadingsSource` (interfaz + SupabaseReadingsSource + SimulatedReadingsSource) a `foundation`; `dashboard` conserva `ReadingsService`+`computePlantStatus` sobre el adaptador compartido. Sin cambio de topología ni ciclo. MEDIUM: U4 nota que el sub-widget de sensores en PlantDetail se compone desde U5; añadir PlantCard/ZoneCard a foundation. LOW: alerts depends_on explícito [foundation,auth,plants,sensors,dashboard] por paridad; suavizar justificación de auth; nombrar almacenamiento del toggle push (config/local en foundation).

- 2026-07-19T17:05:00Z — Reviewer 2 veredicto READY. Cierres no bloqueantes aplicados: (a) sincronizado `alerts` depends_on en unit-of-work.md (línea + tabla) con el yaml [foundation,auth,plants,sensors,dashboard]; (b) el mockup §5 SÍ muestra "humedad actual" a nivel de planta en PlantDetail, así que en vez de quitarlo, se resolvió la dependencia moviendo también la función pura `computePlantStatus` a `foundation` (junto a ReadingsSource); así `plants`/`sensors`/`dashboard` derivan/agrupan sin depender entre sí (sin ciclo). dashboard conserva la pantalla Dashboard + hooks + polling.

## Tradeoffs

## Open questions
