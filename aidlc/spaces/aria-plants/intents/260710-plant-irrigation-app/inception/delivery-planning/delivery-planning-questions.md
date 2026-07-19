# Delivery Planning — Preguntas de Secuenciación

> Etapa 2.8 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Fuente de verdad para la traza de decisiones. Rellena cada `[Answer]:`.
> Cada pregunta ofrece opciones A–E según aplique + `X. Other (please specify)`.
> Deriva de `units-generation/` (unit-of-work + DAG + story-map), `requirements.md`,
> `stories.md`, `mockups.md`, `components.md` y `team.md`/`team-practices.md`.
> El DAG (2.7) da la topología; aquí se elige la **ruta económica** (Bolts).

---

## Q1 — Heurística de secuenciación (tras el walking skeleton)

El walking skeleton como Bolt 1 (gated, solo) está **mandado** por las prácticas.
Para el RESTO de los Bolts, ¿qué heurística?

- A. **Híbrido walking-skeleton-first + dependencia/riesgo**: Bolt 1 = WS; luego los
     demás en orden de dependencias del DAG, adelantando riesgo donde aplique
     (alertas/push van tarde por depender de dashboard). (recomendado)
- B. **Value-first** tras el WS (priorizar features de mayor valor de usuario).
- C. **Risk-first** estricto.
- X. Other (please specify)

[Answer]: A

## Q2 — Granularidad de Bolts

- A. **Híbrido**: Bolt 1 = rebanada fina que cruza unidades (walking skeleton);
     Bolts siguientes **agrupan unidades afines** (p. ej. zones+plants, sensors+dashboard). (recomendado)
- B. **Una unidad por Bolt** (más Bolts pequeños).
- C. **Rebanadas finas** siempre (vertical por historia).
- X. Other (please specify)

[Answer]: A

## Q3 — Paralelismo de Bolts

`team.md` valora el trabajo en paralelo (ramas cortas); el equipo son 2 personas.

- A. **Pipeline serial** con una excepción: `profile` (independiente en el DAG)
     puede desarrollarlo el **junior en paralelo** mientras el senior avanza la
     cadena zones→plants→sensors→dashboard→alerts. (recomendado)
- B. **Estrictamente serial** (un Bolt a la vez).
- C. **Máximo paralelismo** donde el DAG lo permita.
- X. Other (please specify)

[Answer]: A

## Q4 — Reparto de Bolts (senior / junior)

Según `team.md` (senior toma WS + Bolts pesados/integración; junior toma ligeros
autocontenidos, **gated** con revisión del senior).

- A. **Según team.md**: senior = Bolt 1 (WS) + Bolts de modelo de datos e
     integración (plants/zones, sensors/dashboard, alerts+push); junior = Bolts
     ligeros (profile/i18n), gated con revisión del senior. (recomendado)
- B. Todo el senior (el junior solo observa).
- C. Otro reparto (indícalo).
- X. Other (please specify)

[Answer]: A

## Q5 — Frontera del MVP (qué "Should" entran)

Las historias tienen 18 Must + 6 Should (editar/eliminar zona HU-D3/D4, editar/
eliminar planta HU-B5/B6, reasignar planta HU-D5, push HU-F3).

- A. **MVP = todos los Must**; los Should entran en Bolts posteriores **si hay
     tiempo**; **push (HU-F3) se puede diferir** post-MVP sin bloquear (las alertas
     in-app cubren el valor). (recomendado)
- B. **Incluir todos los Should** en el MVP (más alcance, más tiempo).
- C. Incluir solo algunos Should (indica cuáles).
- X. Other (please specify)

[Answer]: A

## Q6 — Dependencias externas (confirmar el mapa)

- A. **Confirmar**: Supabase (proyecto gestionado — disponible); **contrato de
     sensores** (C-4) pendiente → mitigado con el simulador `ReadingsSource`;
     **mecanismo de push** (OQ-4) diferido a NFR/Infra Design; sin hand-offs de
     otros equipos. (recomendado)
- B. Hay otras dependencias externas (indícalas).
- X. Other (please specify)

[Answer]: A
