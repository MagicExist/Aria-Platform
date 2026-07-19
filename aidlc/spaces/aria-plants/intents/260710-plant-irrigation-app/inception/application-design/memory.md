# Application Design — Stage Diary

> Observation log maintained by the orchestrator while running this stage.
> Four standard headings. ISO 8601 timestamps. Read before the approval gate
> for the §13 learnings ritual.

## Interpretations

- 2026-07-19T16:10:00Z — Soporte aws-platform-agent adaptado: este proyecto NO usa AWS (project.md Tech Stack). La perspectiva "plataforma" se aplica a Supabase (BaaS gestionado): Postgres + Auth + RLS + Storage. No se introducen supuestos de AWS.
- 2026-07-19T16:10:00Z — Esta etapa resuelve las open questions arrastradas: OQ-1 (simulación de lecturas), OQ-2 (almacenamiento foto), OQ-3 (borrado cascada), OQ-refined-1 (dónde se computa la agregación multi-sensor), C-4 (contrato de sensores tras adaptador).

## Deviations

- 2026-07-19T16:40:00Z — Reviewer 1 (aidlc-architecture-reviewer-agent) NOT-READY (1 blocker, 3 major, 5 minor). Correcciones: (BLOCKER) ReadingsService necesita lookup plant→sensores → arista ReadingsService→SensorService en matriz/mermaid + nota en methods; (MAJOR) AlertService→PlantService/SensorService para enumerar; (MAJOR) persistencia de alertas → tabla Postgres `alerts` con RLS (dedup cross-poll) + ADR-010; (MAJOR) suavizado "sin procesos desplegables" → push puede requerir función Supabase-side (Edge Function/trigger), cross-ref OQ-4; (minors) quitar lib/supabase de feature/auth, AlertList=wrapper de AlertItem, inventario UI aclarado (atoms en ui/, compuestos en su feature), HU-D5 canónico = PlantService.updatePlant({zoneId}), FK Plant→Zone ON DELETE RESTRICT.

## Tradeoffs

- 2026-07-19T16:20:00Z — Q7=C (foto SOLO local): el usuario eligió almacenamiento local en vez de Supabase Storage (mi recomendación A). Consecuencia documentada en decisions.md ADR-007: la foto no se sincroniza, se pierde al reinstalar/cambiar de dispositivo y no queda bajo RLS. Aceptado como simplificación MVP, reversible a Storage post-MVP. El campo de la planta guarda un URI local, no una clave de objeto remoto.

## Open questions

- 2026-07-19T16:20:00Z — Contrato real de sensores (C-4) sigue sin definirse fuera de este repo; el diseño lo aísla tras `ReadingsSource` (Q4=A) con un simulador. Cuando exista el contrato, solo cambia `SupabaseReadingsSource`, no la app.
