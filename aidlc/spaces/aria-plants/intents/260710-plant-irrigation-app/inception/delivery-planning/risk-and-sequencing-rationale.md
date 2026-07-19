# Risk & Sequencing Rationale — Aria Plants (Android MVP)

> Etapa 2.8 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> El "porqué" del orden de Bolts de `bolt-plan.md`. Deriva del DAG de
> `unit-of-work-dependency.md`, `requirements.md`, `stories.md`, `components.md` y
> `team.md`/`team-practices.md`. Heurística principal: **walking-skeleton-first
> (Cockburn)** + secuenciación por **dependencia/riesgo** (tipo WSJF/Reinertsen CD3).

## Heurística elegida (Q1=A)

1. **Walking skeleton primero (Cockburn)** — mandado por prácticas (org/team/project:
   "ALWAYS run the walking skeleton as a gated, solo Bolt 1"). El Bolt 1 no maximiza
   valor de features sino **reducción de riesgo de integración**: prueba de una vez
   Supabase Auth, RLS, esquema Postgres, render Android y la ruta de lectura por
   polling. Es el mayor desconocido técnico del proyecto (stack nuevo, contrato de
   sensores aún inexistente).
2. **Resto por dependencia + riesgo** — tras el skeleton, se sigue el orden natural
   del DAG (catálogo → monitoreo → alertas), adelantando riesgo donde el DAG lo
   permite. No hay conflicto valor/dependencia que fuerce desviarse del orden
   topológico (ver abajo).

## Puntuación cualitativa (WSJF-lite)

Sin métricas formales (equipo de 2, MVP); puntuación cualitativa
`(valor + criticidad + reducción-de-riesgo) ÷ tamaño`:

| Bolt | Valor usuario | Criticidad | Reducción de riesgo | Tamaño | Prioridad |
|------|---------------|-----------|---------------------|--------|-----------|
| 1 WS | Medio (flujo mínimo) | **Alta** | **Muy alta** (prueba toda la arquitectura) | M | **1ª** |
| 2 Catálogo | Alto (CRUD real) | Media | Media (cascade, zona-primero) | L | 2ª |
| 3 Monitoreo | **Alto** (el propósito: ver humedad) | Media | Media (agregación, frescura) | L | 3ª |
| 4 Alertas | Alto (aviso proactivo) | Media | Media/alta (dedup, push) | M | 4ª |
| 5 Perfil | Bajo/medio | Baja | Baja | S | Paralelo (junior) |

El skeleton gana por **reducción de riesgo** pese a valor de feature medio; alertas
va tarde por depender de dashboard y por concentrar el riesgo técnico restante (push).

## Conformidad con el DAG (topología de 2.7)

Orden de Bolts vs orden topológico del DAG
(`foundation→auth→{zones→plants→sensors→dashboard→alerts, profile}`):

- Bolt 1 (WS) toma una rebanada fina de foundation+auth+zones+plants+sensors+dashboard
  — **prefijo topológico válido** (no viola ninguna dependencia; cada capa mínima se
  apoya en la anterior).
- Bolts 2→3→4 siguen el orden del DAG (zones/plants → sensors/dashboard → alerts).
- Bolt 5 (profile) solo depende de foundation+auth → puede ir en paralelo.

**No hay desviación del orden topológico** que justificar: la secuencia económica
coincide con la topológica salvo el "adelanto fino" del skeleton, que es la práctica
mandada y no rompe dependencias (construye versiones mínimas, no completas, de las
unidades intermedias).

## Riesgos y mitigaciones

| Riesgo | Bolt | Mitigación |
|--------|------|------------|
| Integración Supabase Auth + RLS en Android no probada | 1 | Es el objetivo del walking skeleton; gate antes de seguir |
| **Contrato de sensores inexistente (C-4)** | 1, 3 | Adaptador `ReadingsSource` + simulador (ADR-004); la app no se bloquea |
| Cascade de borrado / zona-primero mal modelados | 2 | FK Postgres (cascade / RESTRICT) + confirmaciones UI (ADR-006) |
| Agregación multi-sensor ambigua | 3 | Regla worst-case ya decidida y persistida (ADR-005); confirmar en Functional Design |
| **Push con app cerrada** requiere pieza server-side | 4 | Diferido (ADR-011); in-app cubre el valor; push es Should |
| Desnivel senior/junior | todos | Reparto por experiencia; Bolts del junior gated (revisión) |

## Definición de la frontera del MVP (Q5=A)

MVP = **todos los Must** (Bolts 1–4 + esenciales de perfil). Los **Should**
(HU-D3/D4, HU-B5/B6, HU-D5, HU-F3) entran en sus Bolts **si hay tiempo**; **push
(HU-F3) es diferible** post-MVP sin bloquear el objetivo (las alertas in-app cubren
el aviso).
