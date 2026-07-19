# RAID Log — App de Monitoreo de Riego de Plantas

> Etapa 1.3 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Risks, Assumptions, Issues, Dependencies. Derivado del `intent-statement.md`,
> del `feasibility-assessment.md` y del `constraint-register.md`.
> Escala de severidad/probabilidad: Baja / Media / Alta.

## Risks

| ID | Riesgo | Prob. | Impacto | Tratamiento |
|----|--------|-------|---------|-------------|
| R-1 | El contrato de datos de sensores cambia respecto al asumido | Media | Medio | Aislar el acceso a datos tras una capa/adaptador; usar datos simulados con forma versionada |
| R-3 | La latencia de alertas con polling resulta insuficiente | Baja | Bajo | Intervalo configurable; migrar a Supabase Realtime si hace falta |
| R-4 | Curva de aprendizaje del stack retrasa el avance | Media | Bajo | Tiempo flexible; alcance MVP pequeño; stack bien documentado |
| R-5 | Exposición accidental de llaves de Supabase | Baja | Alto | Solo anon key en cliente; service key en entorno seguro; RLS activo |

> R-2 (iOS falla en runtime sin pruebas locales) **eliminado** el 2026-07-10: al
> reducir el alcance a solo Android, iOS ya no se construye ni distribuye.

## Assumptions

| ID | Supuesto | Validación pendiente |
|----|----------|----------------------|
| A-1 | Supabase es el backend gestionado definitivo (Postgres + Auth + API) | Confirmado por el usuario (P4) |
| A-2 | Las lecturas de sensores llegarán a Supabase por un proceso externo a este repo | Confirmar cuando exista la fuente de sensores |
| A-3 | Las variables del MVP son humedad y luz (crudas, sin "salud" derivada) | intent-statement (P3) |
| A-4 | Un solo usuario y una sola zona de riego al inicio; escalable después | intent-statement (P5) |
| A-5 | Los free/low tiers de Supabase y Expo EAS cubren el MVP | Revisar límites si crece el uso |
| A-6 | Polling es aceptable para la frescura de datos del MVP | Revisar con uso real (R-3) |

## Issues

| ID | Issue (abierto hoy) | Acción |
|----|---------------------|--------|
| I-1 | No existe aún un esquema definido de las tablas/lecturas de sensores | Definir en Application Design (Inception) contra contrato asumido |
| I-2 | No hay sensores físicos disponibles todavía | Usar datos simulados (seed) en Supabase para desarrollo |

## Dependencies

| ID | Dependencia | Tipo | Estado |
|----|-------------|------|--------|
| D-1 | **Contrato de datos de sensores** (esquema + cómo llegan a Supabase) | Externa | Por definir — dependencia crítica a rastrear |
| D-2 | Cuenta/proyecto de **Supabase** (Postgres + Auth) | Externa (servicio) | Por crear |
| D-3 | **Expo / EAS** para construir el APK de Android | Externa (servicio) | Por configurar |
| D-4 | Proceso de ingesta sensores → Supabase (otro repo/proyecto) | Externa | Fuera de alcance de este repo |

## Resumen

Sin bloqueadores para el MVP. La dependencia crítica es **D-1 (contrato de datos
de sensores)**; mientras no exista, se trabaja con datos simulados (I-2/A-2). Todos
los riesgos tienen tratamiento definido y probabilidad/impacto manejables.
