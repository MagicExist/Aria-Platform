# Application Design — Services

> Etapa 2.6 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Servicios de dominio que envuelven **Supabase** (Q2=A), derivados de
> `requirements.md`, `stories.md` y `team-practices.md`. **Sin AWS** (`project.md`):
> no hay servidor propio; Supabase (Postgres + Auth + RLS) es el backend gestionado
> y la app cliente contiene ÚNICAMENTE la app (C-2). La ingesta de lecturas es
> externa; la app **solo lee** (C-2, C-3 polling).

## Naturaleza de los servicios

Los "servicios" son **módulos cliente** (singletons sin estado) dentro de la app
RN para los flujos de CRUD y dashboard: **no hay procesos desplegables propios de
la app** para esos flujos. Cada uno envuelve el cliente Supabase y expone una API
de dominio tipada. No hay orquestador central: cada feature usa sus hooks de React
Query que llaman a su servicio (**coreografía**, no orquestación).

> **Excepción push (HU-F3)**: entregar una notificación "aunque la app esté cerrada"
> NO lo puede hacer un modelo puramente cliente por polling (no corre JS con el
> proceso inactivo). El mecanismo concreto está **diferido** a NFR/Infra Design
> (OQ-4, S-3) y **probablemente requiera una pieza del lado de Supabase** (p. ej. una
> Edge Function disparada por un trigger de Postgres al insertarse una alerta). Esto
> NO contradice "sin infra propia" (`project.md`): sería una función gestionada por
> Supabase, no un servidor propio. La evaluación in-app por polling cubre las alertas
> **in-app** (HU-F2) aunque el push aún no exista.

## Catálogo de servicios

| Servicio | Responsabilidad | Respaldo Supabase | Consumidores |
|----------|-----------------|-------------------|--------------|
| **AuthService** | Registro/login/logout/sesión, aislamiento por usuario | Supabase **Auth** + RLS | feature/auth, profile |
| **ZoneService** | CRUD de zonas; bloquea borrado con plantas | tabla `zones` (RLS) | zones, plants (selector) |
| **PlantService** | CRUD de plantas; foto local; cascade al borrar | tabla `plants` (RLS) | plants, dashboard |
| **SensorService** | Registrar/asignar/ver sensores por planta | tabla `sensors` (RLS) | sensors, plants |
| **ReadingsService** | Últimas lecturas + agregación worst-case | vía `ReadingsSource` (+ `SensorService` para el lookup planta→sensores) | dashboard, plants |
| **AlertService** | Derivar/entregar/auto-resolver alertas; push | tabla `alerts` (RLS) + `ReadingsService` + `PlantService`/`SensorService` (enumerar) + `config.threshold` + expo-notifications | dashboard, alerts |

## ReadingsSource (adaptador, Q4=A)

Interfaz que aísla el **origen de lecturas** mientras el contrato de sensores (C-4)
no exista:

```
interface ReadingsSource {
  getLatestReadings(sensorIds: string[]): Promise<Reading[]>;
}
```
- `SupabaseReadingsSource` — lee la tabla `readings` (solo lectura) por **polling**.
- `SimulatedReadingsSource` — genera lecturas plausibles para desarrollo (S-2).
- Selección por `config` (env). `ReadingsService` depende de la interfaz, no del origen.

> Cuando exista el contrato real de sensores, **solo cambia `SupabaseReadingsSource`**;
> el resto de la app no se toca.

## Contratos de comunicación

- **App ⇄ Supabase**: SDK JS de Supabase (PostgREST para datos, Auth para sesión).
  Solo la **clave anónima pública** en el cliente (NFR-Seguridad); **RLS** filtra
  por `auth.uid()` en cada tabla (HU-A4).
- **Lecturas**: **pull/polling** (~5 min) vía React Query `refetchInterval`
  (RF-E2, NFR-Frescura). Sin realtime/streaming en el MVP (C-3).
- **Push**: expo-notifications; el token se registra tras conceder permiso
  (Q5=B). Mecanismo exacto → NFR/Infra Design (OQ-4, S-3).

## Ciclo de vida y escalado

- Servicios = singletons cliente, **stateless**; el estado/caché vive en React
  Query. No hay escalado de servidor propio (Supabase gestiona backend/DB).
- **Sesión**: `onAuthStateChange` reinicia la caché de React Query al login/logout
  (evita fugas entre cuentas — refuerza HU-A4 en cliente además de RLS).
- **Errores**: cada servicio lanza errores tipados; React Query reintenta
  transitorios (red) y expone `error` para el `ErrorBanner`.

## Persistencia (resumen)

| Dato | Dónde | Nota |
|------|-------|------|
| Cuenta/sesión | Supabase Auth | política por defecto (S-5) |
| Zonas/Plantas/Sensores | Postgres (RLS por usuario) | FK con `ON DELETE CASCADE` planta→sensor→lectura (Q6=A); FK planta→zona con `ON DELETE RESTRICT` (respalda a nivel DB el bloqueo de `ZoneService.deleteZone` cuando la zona tiene plantas) |
| Lecturas | Postgres `readings` (solo lectura para la app) | ingesta externa (C-2) |
| Alertas | Postgres `alerts` (RLS por usuario) | estado activo/resuelto persistido para de-duplicar entre ciclos de polling y sesiones (HU-F1/F4); ver ADR-010 |
| Foto de planta | **Dispositivo (local)** (Q7=C) | no en Supabase Storage; no sincronizada (ver decisions ADR-007) |
| Config (umbral, polling, tema, idioma) | `config` + `i18n` en la app | umbral 30% fijo (RF-F1) |
