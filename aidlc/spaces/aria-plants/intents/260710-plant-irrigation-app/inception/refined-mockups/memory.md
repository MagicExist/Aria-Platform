# Refined Mockups — Stage Diary

> Observation log maintained by the orchestrator while running this stage.
> Four standard headings. ISO 8601 timestamps. Read before the approval gate
> for the §13 learnings ritual.

## Interpretations

- 2026-07-19T15:23:49Z — Los rough mockups (1.6) ya son detallados (8 pantallas + user-flow); esta etapa REFINA (estados, tokens, design system, accesibilidad, specs de componentes), no re-inventa. Se mantiene dark-first, minimalista, ES/EN, solo humedad, tabs Inicio/Plantas/Zonas/Perfil.

## Deviations

- 2026-07-19T15:40:00Z — Reviewer 1 (aidlc-product-lead-agent) NOT-READY. Correcciones aplicadas: (1) regla de agregación multi-sensor a nivel de planta (status = peor de sus sensores; badge = lectura que dirige ese status) declarada como intención de diseño y marcada como open question para Application Design; (2) PhotoPicker con spec de componente completa + flujo/permiso de foto marcado como diferido a Application/NFR Design (paridad con push); (3) minors: estado defensivo de ZoneSelector aclarado, alerta resuelta con tratamiento visual+a11y, estado Error en Perfil, responsive extendido a PlantCard/HumidityBadge, ZoneCard con tabla propia, specs breves de PlantCheckboxList/LanguageSelector, mención de team-practices en interaction-spec.

## Tradeoffs

- 2026-07-19T15:40:00Z — Agregación multi-sensor: elegí "peor de los sensores" (worst-case) como default de diseño porque el objetivo del producto es avisar cuando ALGUNA parte de la planta se seca; se documenta y se difiere a Application Design la confirmación (RF-F1 "esa planta/sensor" es ambiguo upstream). Alternativas: promedio (oculta un sensor crítico) o último-actualizado (arbitrario).

## Open questions

- 2026-07-19T15:23:49Z — Tensión a resolver en preguntas: los rough mockups permiten "crear zona inline" desde el formulario de planta como bootstrap; la regla learned zona-primero (project.md `## Decided`, cid user-stories:c5) exige que exista ≥1 zona antes de crear planta y sin zona por defecto implícita. La creación inline SÍ satisface la regla (el usuario crea explícitamente la zona), pero el enforcement exacto (inline vs redirección) es decisión de esta etapa. → Resuelto Q1=A (inline).
- 2026-07-19T15:40:00Z — OQ-refined-1: regla exacta de agregación multi-sensor a nivel de planta (status y valor mostrado en Dashboard/lista; qué sensor dispara la alerta HU-F1). El diseño asume worst-case; Application Design debe confirmarlo o ajustarlo. Relaciona con RF-F1 ("esa planta/sensor").
- 2026-07-19T15:40:00Z — OQ-refined-2: permiso de foto/cámara/galería (HU-B2) — mecanismo y momento del prompt, diferido a Application/NFR Design (paridad con push/OQ-4). Almacenamiento de la foto ya es OQ-2.
- 2026-07-19T15:56:00Z — Reviewer 2 (aidlc-product-lead-agent) veredicto READY (ambos majors resueltos). Residuales triviales no bloqueantes a resolver en construcción: (a) desempate de `AlertItem.sensorName` cuando 2+ sensores cruzan a la vez (usar el de lectura más baja, coherente con la regla de valor); (b) responsive solo añadido a HumidityBadge/PlantCard (los de riesgo de truncado); aceptable.
