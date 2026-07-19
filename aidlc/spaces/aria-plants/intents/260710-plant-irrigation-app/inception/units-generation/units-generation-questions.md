# Units Generation — Preguntas de Descomposición

> Etapa 2.7 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Fuente de verdad para la traza de decisiones. Rellena cada `[Answer]:`.
> Cada pregunta ofrece opciones A–E según aplique + `X. Other (please specify)`.
> Deriva de `application-design/` (components, methods, services, dependency,
> decisions), `requirements.md` y `stories.md`. Define SOLO la topología de
> unidades (el orden/secuencia de Bolts es de Delivery Planning 2.8).

---

## Q1 — Estrategia de límite de unidades

¿Cómo trazamos las fronteras de las unidades de trabajo?

- A. **Por feature** (auth, zones, plants, sensors, dashboard, alerts, profile) +
     una unidad **foundation** compartida. Alinea 1:1 con la estructura de código
     (ADR-001) y con las historias por grupo. (recomendado)
- B. **Por dominio agrupado** (menos unidades, más gruesas; p. ej. "cuentas",
     "catálogo plantas/zonas/sensores", "monitoreo").
- C. Otra (indícala).
- X. Other (please specify)

[Answer]: A

## Q2 — Granularidad de las unidades

- A. **Fina**: una unidad por feature → más unidades pequeñas, mejor reparto
     senior/junior (`team.md`: el junior toma unidades ligeras y autocontenidas). (recomendado)
- B. **Gruesa**: pocas unidades grandes (menos overhead, menos paralelismo).
- C. **Media**: agrupar features muy afines (p. ej. dashboard+alerts).
- X. Other (please specify)

[Answer]: A 

## Q3 — Unidad "foundation" compartida

¿Extraemos una base común de la que dependan las demás?

- A. **Sí**: unidad `foundation` con cliente Supabase (anon key), `config` (umbral,
     polling), i18n (ES/EN), tema RN Paper (dark-first), navegación y primitivos UI
     (EmptyState/ErrorBanner/LoadingSkeleton). Todas dependen de ella. (recomendado)
- B. **No**: distribuir lo compartido dentro de cada unidad (más duplicación).
- X. Other (please specify)

[Answer]: A

## Q4 — Contratos / integración entre unidades

¿Cómo se integran las unidades entre sí?

- A. **Vía la capa de servicios + tipos TS compartidos** (foundation expone el
     cliente Supabase y los tipos de dominio; cada unidad expone su servicio/hooks).
     La app es un solo binario, integración en compilación. (recomendado)
- B. Contratos más formales (eventos/inyección) — probablemente over-engineering para el MVP.
- X. Other (please specify)

[Answer]: A

## Q5 — Paralelismo en el DAG (topología, no orden)

¿Permitimos que unidades independientes se puedan construir en paralelo?

- A. **Sí**: DAG con paralelismo entre unidades sin dependencia mutua (2.8 elegirá
     el orden económico y el walking skeleton). Habilita trabajar senior/junior en
     paralelo. (recomendado)
- B. **No**: cadena estrictamente topológica y secuencial.
- X. Other (please specify)

[Answer]: A
