# Bolt Plan — Aria Plants (Android MVP)

> Etapa 2.8 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Secuencia económica de Bolts derivada del DAG de `units-generation/`
> (`unit-of-work.md`, `unit-of-work-dependency.md`, `unit-of-work-story-map.md`),
> `requirements.md`, `stories.md`, `mockups.md`, `components.md` y las prácticas de
> `team-practices.md`/`team.md`. Heurística: **walking-skeleton-first + dependencia/
> riesgo** (Q1=A); granularidad **híbrida** (Q2=A). El DAG da la topología; este
> plan elige el camino. La justificación del orden está en
> `risk-and-sequencing-rationale.md`.

## Resumen de la secuencia

| Bolt | Nombre | Unidades | WS | Dueño | Should incluidos (si hay tiempo) |
|------|--------|----------|----|-------|----------------------------------|
| 1 | Walking Skeleton | foundation + auth + zones/plants/sensors/dashboard (rebanada fina) | ✅ | Senior (solo, **gated**) | — |
| 2 | Catálogo completo | zones, plants | — | Senior | HU-D3, HU-D4, HU-B5, HU-B6, HU-D5 |
| 3 | Monitoreo completo | sensors, dashboard | — | Senior | — |
| 4 | Alertas | alerts | — | Senior | HU-F3 (push, diferible) |
| 5 | Perfil & Ajustes | profile | — | **Junior** (**gated**, paralelizable) | — |

Pipeline **serial** salvo Bolt 5 (`profile`), independiente en el DAG, que el
junior puede desarrollar en paralelo (Q3=A). Frontera del MVP (Q5=A): **todos los
Must**; los Should entran en Bolts posteriores si hay tiempo; **push (HU-F3) es
diferible** post-MVP (las alertas in-app cubren el valor).

---

## Bolt 1 — Walking Skeleton  ✅ (gated, solo)

- **Unidades (rebanada fina)**: `foundation` + `auth` + versiones mínimas de
  `zones`, `plants`, `sensors`, `dashboard`.
- **Capas de arquitectura que prueba**: Supabase **Auth** (registro/login/sesión),
  **Postgres + RLS** (aislamiento por usuario), esquema base (zonas/plantas/
  sensores/lecturas), render en **Android** (Expo/RN + RN Paper dark), el
  adaptador **`ReadingsSource`** (simulador) + **polling** + agregación
  `computePlantStatus`.
- **Historias (Must, mínimas)**: HU-A1, HU-A2, HU-A4 (aislamiento), HU-D1 (crear
  zona), HU-B1 (crear planta con zona), HU-C1 (asignar sensor), HU-E1/HU-E2 (ver
  humedad por polling). i18n base (HU-X1 infra) y tema oscuro.
- **Definition of Done**: un usuario se registra, inicia sesión, crea una zona,
  crea una planta en esa zona, le asigna un sensor y ve su humedad en el dashboard
  (con lecturas del simulador). RLS verificado con 2 cuentas. Tests junto al código
  (~80% líneas), CI verde.
- **Hipótesis de confianza**: *la arquitectura Expo + Supabase funciona de extremo
  a extremo en Android* — auth, aislamiento RLS, ruta de lectura por polling y
  render. Si esto se sostiene, el resto de los Bolts son incremento de features.
- **Demo esperada**: flujo login→zona→planta→sensor→humedad en un APK sobre un
  dispositivo Android.
- **Gate**: SÍ (walking skeleton siempre gated). Tras aprobarse, se dispara el
  **prompt de escalera** (autónomo vs gate por Bolt) para el resto.

## Bolt 2 — Catálogo completo (zones + plants)  (senior)

- **Unidades**: `zones`, `plants` (completan lo que el WS dejó mínimo).
- **Historias**: **Must** — HU-B3 (listado), HU-B4 (detalle completo), HU-B2 (foto
  local), HU-D2 (ver zonas). **Should (si hay tiempo)** — HU-D3 (editar zona),
  HU-D4 (eliminar zona, bloqueada con plantas), HU-B5 (editar planta), HU-B6
  (eliminar planta, cascade), HU-D5 (reasignar planta).
- **DoD**: CRUD completo de plantas y zonas con las reglas (zona-primero, cascade,
  foto local), estados de UI (loading/empty/error), tests + CI verde.
- **Hipótesis de confianza**: la gestión completa del catálogo (incluida la
  cascade y la regla zona-primero) se comporta como el diseño lo especifica.
- **Demo**: crear/editar/eliminar plantas y zonas; foto local; mover planta de zona.

## Bolt 3 — Monitoreo completo (sensors + dashboard)  (senior)

- **Unidades**: `sensors`, `dashboard`.
- **Historias (Must)**: HU-C2 (ver sensores + última lectura, multi-sensor),
  HU-E1/HU-E2 dashboard completo (agregación worst-case, estados sin-dato/error,
  refresco por polling y pull-to-refresh).
- **DoD**: dashboard y detalle muestran humedad agregada por planta y por sensor,
  con todos los estados; polling ~5 min; tests + CI verde.
- **Hipótesis de confianza**: la agregación multi-sensor (worst-case) y la frescura
  por polling se comportan correctamente con varias plantas/sensores.
- **Demo**: dashboard con varias plantas, estados OK/BAJA/Sin dato; detalle con 2+ sensores.

## Bolt 4 — Alertas (alerts)  (senior, integración)

- **Unidades**: `alerts`.
- **Historias (Must)**: HU-F1 (generar alerta < 30%), HU-F2 (lista in-app), HU-F4
  (auto-resolución). **Should/diferible** — HU-F3 (push).
- **DoD**: tabla `alerts` (RLS) con dedup entre polls; lista de alertas recientes
  activas; auto-resolución al recuperar; (push in-app + priming si entra). Tests + CI.
- **Hipótesis de confianza**: la derivación/dedup/auto-resolución de alertas es
  correcta entre ciclos de polling; la entrega in-app es fiable aun sin push.
- **Demo**: humedad baja → alerta en "recientes" (+ push si está); recuperación → resuelta.

## Bolt 5 — Perfil & Ajustes (profile)  (junior, gated, paralelizable)

- **Unidades**: `profile` (ligera, autocontenida — buen candidato junior, `team.md`).
- **Historias**: HU-X1 (selector de idioma ES/EN, UI), HU-A3 (cerrar sesión, botón),
  HU-F3 (toggle de push, control), umbral informativo (RF-F1), tema (oscuro fijo).
- **DoD**: pantalla de perfil/ajustes con idioma, toggle push, umbral informativo y
  logout; i18n sin strings hardcodeados; tests + CI verde.
- **Hipótesis de confianza**: los ajustes (i18n, toggle push, logout) funcionan de
  forma autocontenida sin acoplarse a la cadena de features.
- **Demo**: cambiar idioma ES/EN, activar/desactivar push, cerrar sesión.
- **Gate**: SÍ (Bolt del junior — revisión del senior antes de integrar, `team.md`).

## Notas de ejecución

- **Autonomía de Construcción**: se decide con el **prompt de escalera** tras
  aprobar el Bolt 1 (autónomo vs gate por Bolt). Los Bolts del junior (Bolt 5) van
  **gated** independientemente del modo (revisión del senior, `team.md`).
- **Merge**: squash-merge de cada Bolt a `main` (una rama corta por Bolt, `team.md`).
- **Iteración de diseño de Construcción**: **unit-major** (se autoran los 4 docs de
  diseño de una unidad antes de pasar a la siguiente), por ser un plan
  walking-skeleton-first / Bolt-based con coherencia de diseño por unidad.
