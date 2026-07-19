# User Stories — Assessment (¿Se necesitan historias de usuario?)

> Etapa 2.4 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-14
> Deriva de `requirements.md` (2.3) y `team-practices.md` (2.2).

## Decisión

**EXECUTE** — se generan personas + historias de usuario.

## Rationale

Aria Plants es una **app móvil de cara al usuario** con flujos de interacción
diferenciados (autenticación, CRUD de plantas, gestión de sensores, zonas de
riego, dashboard de humedad y alertas). Las historias de usuario aportan valor
directo aquí:

- **Feature de cara al usuario**: cada requisito funcional (RF-A…RF-F) se traduce
  en interacciones concretas de UI que conviene expresar como valor para un actor.
- **Lógica de negocio con matices**: aislamiento por usuario (RLS), umbral fijo de
  humedad con auto-resolución de alertas, polling ~5 min — reglas que se benefician
  de criterios de aceptación verificables por historia.
- **Base para Refined Mockups (2.5) y Delivery Planning (2.8)**: las historias
  priorizadas por MoSCoW alimentan la frontera del MVP y la secuencia de Bolts
  (walking skeleton: login → registrar planta → asignar sensor → ver humedad).

## Factores considerados

| Factor | Evaluación |
|--------|------------|
| Tipo de proyecto | Greenfield, app móvil Android (Expo/RN + TS) |
| Alcance de cara al usuario | Alto — toda la app es interfaz de usuario |
| Señales de complejidad | Media — multi-entidad (usuario/zona/planta/sensor/lectura), RLS, alertas, polling |
| Personas | Principalmente **una** (cuidador doméstico); diseño permite crecer (S-1) |
| Coordinación cross-team | Baja (equipo de 2), pero hay una frontera app↔backend de sensores (contrato por definir, C-4) |

## Áreas donde las historias aportan más valor

1. **Autenticación y aislamiento de datos** (RF-A) — el punto de integración más
   delicado del walking skeleton.
2. **Registro de planta + asignación de sensor + ver humedad** (RF-B, RF-C, RF-E)
   — la rebanada de extremo a extremo del walking skeleton.
3. **Alertas de humedad baja** (RF-F) — regla de negocio con estados (dispara /
   se auto-resuelve) que necesita criterios de aceptación claros.
4. **Zonas de riego y organización** (RF-D) — agrupación que estructura el dashboard.

## Cobertura alternativa (si se hubiese omitido)

No aplica — se ejecuta. (Si se omitiera, `requirements.md` por sí solo no captaría
el flujo de valor por actor ni la priorización MoSCoW que necesita Delivery
Planning.)
