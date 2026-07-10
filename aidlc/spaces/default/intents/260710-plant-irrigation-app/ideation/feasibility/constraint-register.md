# Constraint Register — App de Monitoreo de Riego de Plantas

> Etapa 1.3 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Restricciones que acotan el diseño. Derivadas del `intent-statement.md` y de
> `feasibility-questions.md`. Cada restricción se arrastra a Inception.

## Restricciones Técnicas

| ID | Restricción | Origen | Impacto en diseño |
|----|-------------|--------|-------------------|
| TC-1 | La app debe funcionar en web, Android e iOS desde **un solo código base** | intent-statement + P1 | Fuerza un framework cross-platform (Expo/React Native) |
| TC-2 | Stack: **React + Expo + TypeScript** | P1, P2 | Lenguaje y tooling fijados; librerías del ecosistema RN/Expo |
| TC-3 | **iOS no se puede verificar localmente** (sin Mac) | intent-statement | iOS = build no verificado; pruebas en Android + web |
| TC-4 | Este repo contiene **solo la app**; el backend es Supabase (gestionado) | intent-statement P6 + P4 | La app consume Supabase directo; sin backend propio en este repo |
| TC-5 | Los datos de sensores se leen por **polling**, no push | P3 | UI y capa de datos diseñadas para refresco periódico |
| TC-6 | **Contrato de datos de sensores por definir** | intent-statement + feasibility | Diseñar contra contrato asumido + datos simulados hasta que exista |
| TC-7 | Autenticación y base de datos vía **Supabase (Auth + Postgres + RLS)** | P4 | Sin auth propia; autorización con Row Level Security |
| TC-8 | Distribución del MVP = **Android APK** (sideload), sin tiendas | P5 | Empaquetado APK (EAS/local); web/iOS opcionales |

## Restricciones Organizacionales

| ID | Restricción | Origen | Impacto |
|----|-------------|--------|---------|
| OC-1 | Proyecto **personal**; un único responsable (autor = usuario = decisor) | intent-capture | Decisiones y aprobaciones inmediatas; sin coordinación de equipo |
| OC-2 | **Presupuesto pequeño**; preferencia por tiers gratuitos/baratos | P6 | Elegir servicios con free tier (Supabase, Expo EAS) |
| OC-3 | Tiempo **flexible**, sin fecha límite | P6 | No hay presión de cronograma sobre el alcance |
| OC-4 | Objetivo de **aprendizaje/exploración** | intent-capture | Se acepta invertir tiempo en aprender el stack elegido |

## Restricciones Regulatorias / Cumplimiento

| ID | Restricción | Origen | Impacto |
|----|-------------|--------|---------|
| RC-1 | Solo datos personales del autor; **nada sensible ni de terceros** | P7 | Compliance mínimo; sin marcos obligatorios |
| RC-2 | No hardcodear secretos; usar variables de entorno | Guardrails de construcción | Solo la anon key de Supabase en el cliente; service key nunca en el cliente |
| RC-3 | Habilitar **RLS** desde el inicio (buena higiene, prepara multiusuario) | Recomendación compliance | Autorización por fila aunque haya un solo usuario |

## Notas

- Ninguna de estas restricciones bloquea el MVP.
- TC-6 (contrato de sensores) es la más relevante y se rastrea también como
  dependencia en `raid-log.md`.
- La ausencia de AWS es intencional (ver `feasibility-assessment.md` §Perspectiva
  de Plataforma); el diseño posterior no debe introducir supuestos de AWS.
