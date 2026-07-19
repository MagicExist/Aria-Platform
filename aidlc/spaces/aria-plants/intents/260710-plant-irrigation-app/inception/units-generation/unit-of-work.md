# Units of Work — Aria Plants (Android MVP)

> Etapa 2.7 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Descomposición en unidades de trabajo derivada de `application-design/`
> (`components.md`, `component-methods.md`, `services.md`, `component-dependency.md`,
> `decisions.md`), `requirements.md` y `stories.md`. Decisiones Q1–Q5: por feature
> + `foundation`, granularidad fina, contratos por capa de servicios, DAG con
> paralelismo. **Topología solamente**: el orden de construcción y el walking
> skeleton los decide Delivery Planning (2.8).

## Modelo de despliegue (común a todas)

La app es un **único APK Android** (EAS/local, `team-practices.md`): todas las
unidades se **integran en una sola app** (embedded). "Unidad" = límite de
trabajo/Bolt, **no** de despliegue independiente. Backend Supabase gestionado
(sin infra propia, `project.md`).

## Unidades

### U1 · foundation
- **Descripción/boundary**: base compartida de la que dependen todas las demás.
- **Responsabilidad (posee)**: cliente Supabase (solo anon key — NFR-Seguridad), `config` (umbral 30% RF-F1, intervalo de polling, selección de `ReadingsSource`), i18n (i18next ES/EN, español por defecto), tema React Native Paper (MD3, dark-first) + tokens, shell de navegación (tabs + stacks), el **adaptador `ReadingsSource`** (interfaz + `SupabaseReadingsSource` + `SimulatedReadingsSource`, ADR-004 — primitiva genérica de lectura, análoga a `lib/supabase`, sin lógica de dashboard), la **función pura de agregación `computePlantStatus`** (worst-case, ADR-005 — pura, sin dependencias, compartida por `plants`/`sensors`/`dashboard`), y los primitivos UI compartidos (`EmptyState`, `ErrorBanner`, `LoadingSkeleton`, `HumidityBadge`, `PlantCard`, `ZoneCard`, `FormField`, `ConfirmDialog`, `LanguageSelector`).
- **Kind**: `library` · **Complejidad**: S · **Despliegue**: embedded.
- **Notas**: única unidad que importa el SDK de Supabase (ADR-002). El `ReadingsSource` vive aquí para que tanto `sensors` (última lectura por sensor, HU-C2) como `dashboard` (agregación) lean sin depender uno del otro (evita un ciclo). No expone pantallas de usuario por sí sola.

### U2 · auth
- **Descripción/boundary**: cuentas, sesión y aislamiento por usuario.
- **Responsabilidad**: `AuthService`, pantallas Login/Registro, `AuthProvider` (contexto de sesión + reset de caché al cambiar de sesión), priming de permiso push (una vez tras login).
- **Kind**: `ui` · **Complejidad**: M · **Depende de**: foundation.
- **Notas**: RLS por usuario vive en la DB (Supabase); el cliente refuerza el aislamiento reseteando la caché (HU-A4).

### U3 · zones
- **Descripción/boundary**: zonas de riego (base de la regla zona-primero).
- **Responsabilidad**: `ZoneService`, pantallas ZoneList/ZoneForm (crear/editar/eliminar), `PlantCheckboxList` para asignación.
- **Kind**: `ui` · **Complejidad**: M · **Depende de**: foundation, auth.
- **Notas**: `deleteZone` bloquea si la zona tiene plantas (ADR-006). ZoneForm es invocable inline desde el formulario de planta (zona-primero, Q1=A refined).

### U4 · plants
- **Descripción/boundary**: CRUD de plantas + foto local + enforcement zona-primero.
- **Responsabilidad**: `PlantService` (escritor canónico de `plants.zone_id`, HU-D5), pantallas PlantList/PlantDetail/PlantForm, `PhotoPicker` (local, Q7=C/ADR-007), `ZoneSelector` (con "+ crear zona" inline).
- **Kind**: `ui` · **Complejidad**: L · **Depende de**: foundation, auth, zones.
- **Notas**: Guardar deshabilitado sin nombre y sin zona. Borrado de planta = cascade sensores+lecturas (ADR-006). El sub-componente de **sensores + última lectura** dentro de PlantDetail se implementa y compone desde la unidad `sensors` (ver U5), no aquí; PlantDetail queda con nombre/especie/foto/zona + la **humedad actual a nivel de planta** (la "Humedad actual" del mockup §5). Esa humedad de planta se deriva con la `ReadingsSource` y la función pura `computePlantStatus` **de `foundation`** (no vía `dashboard`), por lo que `plants` NO depende de `dashboard` (evita el ciclo con la arista `dashboard→plants`).

### U5 · sensors
- **Descripción/boundary**: registrar/asignar/ver sensores de una planta.
- **Responsabilidad**: `SensorService`, pantalla AssignSensor, y el sub-componente "lista de sensores + última lectura" que se compone dentro de PlantDetail (usa el `ReadingsSource` de `foundation` para la última lectura por sensor).
- **Kind**: `ui` · **Complejidad**: M · **Depende de**: foundation, auth, plants.
- **Notas**: una planta admite varios sensores (HU-C1). Lee la última lectura por sensor vía el `ReadingsSource` compartido de `foundation` (no depende de `dashboard`). Editar/eliminar sensor = Could (no MVP).

### U6 · dashboard
- **Descripción/boundary**: lectura de humedad y su agregación por planta.
- **Responsabilidad**: `ReadingsService` (sobre el `ReadingsSource` compartido de `foundation`, aplicando la función pura `computePlantStatus` de `foundation`), pantalla Dashboard, hook `usePlantReadings`, polling ~5 min (React Query, ADR-003).
- **Kind**: `ui` · **Complejidad**: L · **Depende de**: foundation, auth, plants, sensors.
- **Notas**: tanto el adaptador `ReadingsSource` como la función pura `computePlantStatus` viven en `foundation` (compartidos con `sensors` y `plants`); aquí queda la **pantalla Dashboard** y su orquestación de lectura por planta. Obtiene los sensores de una planta vía `SensorService`. Solo humedad (RF-E3).

### U7 · alerts
- **Descripción/boundary**: alertas de humedad baja (derivación, entrega, auto-resolución).
- **Responsabilidad**: `AlertService`, tabla Postgres `alerts` (RLS, ADR-010) para dedup/estado, `AlertList` (wrapper de `AlertItem`), auto-resolución (HU-F4), push in-app + registro de push (expo-notifications).
- **Kind**: `ui` · **Complejidad**: M · **Depende de**: foundation, auth, plants, sensors, dashboard.
- **Notas**: la entrega push "con app cerrada" puede requerir una función Supabase-side (ADR-011, diferida a NFR/Infra Design). La lista in-app no depende de esa pieza. `AlertService` enumera plantas y sensores (por eso el depends_on directo a `plants`/`sensors`, además de `dashboard`).

### U8 · profile
- **Descripción/boundary**: perfil y ajustes.
- **Responsabilidad**: pantalla Profile/Settings: selector de idioma (HU-X1), tema (oscuro fijo, informativo), toggle push (`PushToggle`, HU-F3), umbral informativo (RF-F1), cerrar sesión (HU-A3).
- **Kind**: `ui` · **Complejidad**: S · **Depende de**: foundation, auth.
- **Notas**: unidad ligera y autocontenida → buen candidato para el junior (`team.md`); puede ir en paralelo a la cadena zones→plants.

## Resumen

| Unidad | Kind | Compl. | Depende de |
|--------|------|--------|-----------|
| foundation | library | S | — |
| auth | ui | M | foundation |
| zones | ui | M | foundation, auth |
| plants | ui | L | foundation, auth, zones |
| sensors | ui | M | foundation, auth, plants |
| dashboard | ui | L | foundation, auth, plants, sensors |
| alerts | ui | M | foundation, auth, plants, sensors, dashboard |
| profile | ui | S | foundation, auth |

8 unidades (1 library + 7 ui). El detalle de dependencias y el bloque machine-readable están en `unit-of-work-dependency.md`; el mapeo de historias en `unit-of-work-story-map.md`.
