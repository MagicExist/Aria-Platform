# Application Design — Components

> Etapa 2.6 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Estructura técnica de la app derivada de `requirements.md`, `stories.md`,
> `team-practices.md` y los refined mockups (2.5). Stack: **Expo/React Native + TS
> strict**, backend **Supabase** (Postgres + Auth + RLS) — **sin AWS** (`project.md`).
> Organización **por feature** (Q1=A) + capas compartidas. Los componentes hablan
> con Supabase solo a través de la **capa de servicios** (Q2=A).

## Modelo de dominio (referencia)

De `requirements.md`: `Usuario 1-* Zona`, `Zona 1-* Planta`, `Planta 1-* Sensor`,
`Sensor 1-* Lectura` (la app **solo lee** lecturas). Alerta = estado derivado de
las lecturas vs. umbral fijo (30%).

## Capas

```
+-----------------------------------------------------------+
| UI (pantallas + componentes RN Paper, por feature)        |
+-----------------------------------------------------------+
| Estado servidor: React Query hooks (por feature)          |
+-----------------------------------------------------------+
| Servicios de dominio (Auth/Zone/Plant/Sensor/Readings/... |
+-----------------------------------------------------------+
| lib: Supabase client · ReadingsSource adapter · i18n · cfg|
+-----------------------------------------------------------+
| Supabase (Postgres + Auth + RLS)  [externo, gestionado]   |
+-----------------------------------------------------------+
```
<!-- Text fallback: cinco capas de arriba a abajo: UI por feature; hooks de React Query; servicios de dominio; librerías compartidas (cliente Supabase, adaptador de lecturas, i18n, config); y Supabase gestionado como backend externo. -->

## Componentes por feature

### feature/auth
- **Propósito**: registro, login, logout, sesión y aislamiento por usuario (RF-A).
- **Posee**: pantallas Login/Registro, `AuthProvider` (contexto de sesión), priming de permiso push (una vez tras login, Q5=B refined).
- **Interfaz pública**: `useAuth()` (session, user, signIn, signUp, signOut), `<AuthProvider>`.
- **Depende de**: `AuthService` (que es el único que toca `lib/supabase`; la UI nunca llama a Supabase directo — Q2=A).
- **Traza**: HU-A1..A4.

### feature/zones
- **Propósito**: CRUD de zonas de riego y regla **zona-primero** (RF-D, `project.md` Decided).
- **Posee**: pantallas ZoneList / ZoneForm (crear/editar/eliminar), `useZones`, `useZone`.
- **Interfaz pública**: hooks + navegación a ZoneForm (invocable inline desde el form de planta).
- **Depende de**: `ZoneService`.
- **Traza**: HU-D1..D5.

### feature/plants
- **Propósito**: CRUD de plantas, foto (local, Q7=C), detalle con humedad y sensores (RF-B).
- **Posee**: pantallas PlantList / PlantDetail / PlantForm, `PhotoPicker` (local), `usePlants`, `usePlant`.
- **Interfaz pública**: hooks + navegación; el form exige zona (zona-primero).
- **Depende de**: `PlantService`, `ZoneService` (selector de zona), `ReadingsService` (humedad en detalle), `SensorService` (sensores en detalle).
- **Traza**: HU-B1..B6, HU-D2.

### feature/sensors
- **Propósito**: registrar/asignar sensores a una planta y verlos (RF-C).
- **Posee**: pantalla AssignSensor, `useSensorsByPlant`.
- **Interfaz pública**: hooks + navegación desde PlantDetail.
- **Depende de**: `SensorService`.
- **Traza**: HU-C1, HU-C2.

### feature/dashboard
- **Propósito**: vista de inicio con humedad por planta y alertas recientes (RF-E, RF-F2).
- **Posee**: pantalla Dashboard, `usePlantReadings`.
- **Interfaz pública**: hooks de lectura agregada por planta.
- **Depende de**: `ReadingsService` (que hace la **agregación worst-case** `computePlantStatus` en la capa de servicio, no en la UI — Q5=A), `PlantService`, `AlertService`.
- **Traza**: HU-E1, HU-E2, HU-F2.

### feature/alerts
- **Propósito**: derivar y mostrar alertas de humedad baja; entrega push (RF-F).
- **Posee**: `AlertList` (contenedor delgado que compone los `AlertItem` de `interaction-spec.md`, mostrando solo alertas activas), lógica de auto-resolución, integración de push (expo-notifications).
- **Interfaz pública**: `useActiveAlerts`, disparo/registro de push.
- **Depende de**: `AlertService`, `ReadingsService`.
- **Traza**: HU-F1, HU-F2, HU-F3, HU-F4.

### feature/profile
- **Propósito**: perfil y ajustes: idioma (ES/EN), tema (oscuro fijo), toggle push, umbral informativo, cerrar sesión.
- **Posee**: pantalla Profile/Settings.
- **Interfaz pública**: usa `useAuth`, `i18n`, config.
- **Depende de**: `AuthService`, `i18n`, `config`.
- **Traza**: HU-A3, HU-X1, HU-F3.

## Capas compartidas

### lib/supabase
- **Propósito**: instancia única del cliente Supabase con la **clave anónima pública** (NFR-Seguridad: nunca la service key), manejo de sesión y `onAuthStateChange`.
- **Interfaz**: `supabase` (cliente), helpers de sesión.
- **Boundary**: único punto que importa el SDK de Supabase; los servicios lo consumen.

### lib/readings (ReadingsSource adapter — Q4=A)
- **Propósito**: aislar el **origen de lecturas** mientras el contrato de sensores (C-4) no existe.
- **Interfaz**: `ReadingsSource { getLatestReadings(sensorIds): Promise<Reading[]> }`.
- **Implementaciones**: `SupabaseReadingsSource` (tabla `readings` por polling) y `SimulatedReadingsSource` (dev); se selecciona por `config`.
- **Boundary**: `ReadingsService` depende de la interfaz, no del origen. `ReadingsService` obtiene los sensores de una planta vía `SensorService` y computa la agregación worst-case con la función pura `computePlantStatus` (capa de servicio, no UI).

### ui (design system)
- **Propósito**: tema React Native Paper (MD3, dark-first) + **átomos presentacionales genéricos compartidos**: `HumidityBadge`, `PlantCard`, `ZoneCard`, `FormField`, `EmptyState`, `ErrorBanner`, `LoadingSkeleton`, `ConfirmDialog`, `LanguageSelector` (reutilizables entre features).
- **Regla de ubicación**: los 14 componentes de `interaction-spec.md` (2.5) se reparten así — los **genéricos** viven en `ui/`; los **compuestos/de dominio** viven en su feature dueña: `ZoneSelector`/`PhotoPicker`/`PlantCheckboxList` → `feature/plants`/`feature/zones`; `AlertItem`/`AlertList` → `feature/alerts`; `PushToggle` → `feature/profile`.
- **Boundary**: presentacional; sin acceso a datos.

### i18n
- **Propósito**: i18next + `expo-localization`; recursos ES/EN; **sin strings hardcodeados** (`project.md`, NFR-Usabilidad). Idioma por defecto español.

### config
- **Propósito**: constantes de app: **umbral 30%** (RF-F1), intervalo de polling (~5 min), selección de `ReadingsSource`, tema. Centraliza los "números mágicos".

### navigation
- **Propósito**: React Navigation — Tab Navigator (Inicio/Plantas/Zonas/Perfil) + Stacks para auth y formularios full-screen (Q2=A refined).

## Boundaries / reglas de dependencia

- La **UI** nunca llama a Supabase directo; solo a hooks → servicios (Q2=A).
- Solo `lib/supabase` importa el SDK de Supabase.
- La **agregación multi-sensor** vive en el cliente (`computePlantStatus`), no en la UI ni en la DB (Q5=A).
- El **origen de lecturas** está detrás de `ReadingsSource` (Q4=A).
- Las **claves**: solo anon key en cliente (NFR-Seguridad); RLS por usuario en todas las tablas (HU-A4).
