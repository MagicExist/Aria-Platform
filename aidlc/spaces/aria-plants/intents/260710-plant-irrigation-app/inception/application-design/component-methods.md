# Application Design — Component Methods

> Etapa 2.6 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Firmas de la interfaz pública de cada servicio/componente de `components.md`,
> derivadas de `requirements.md`, `stories.md` y `team-practices.md`. TS strict.
> El "qué hace" (las reglas de negocio detalladas van en Functional Design 3.1).
> Manejo de errores: los servicios lanzan **errores tipados**; los hooks de React
> Query los exponen; la UI los mapea a inline/banner (ver `interaction-spec.md`).

## Tipos base (referencia)

```
type Zone = { id: string; name: string; userId: string; createdAt: string };
type Plant = { id: string; name: string; species?: string; photoUri?: string;
               zoneId: string; userId: string; createdAt: string };
type Sensor = { id: string; externalId: string; plantId: string; type: 'humidity' };
type Reading = { sensorId: string; humidity: number; takenAt: string };
type PlantStatus = { status: 'ok' | 'low' | 'no-data'; value: number | null;
                     drivingSensorId?: string };
type Alert = { id: string; plantId: string; sensorId?: string;
               firedAt: string; resolved: boolean };
```

## Errores tipados

`AuthError` · `ValidationError` · `NotFoundError` · `ConflictError` · `NetworkError`.
Regla (guardrail construcción): error en frontera (Supabase/I/O) siempre se
propaga o se registra — nunca falla silenciosa.

## AuthService (RF-A)

| Método | Firma | Propósito | Errores |
|--------|-------|-----------|---------|
| signUp | `(input:{name;email;password}) => Promise<Session>` | Crea cuenta (HU-A1) y autentica | AuthError (email en uso / política S-5) |
| signIn | `(email,password) => Promise<Session>` | Inicia sesión (HU-A2) | AuthError (credenciales) |
| signOut | `() => Promise<void>` | Cierra sesión (HU-A3) | — |
| getSession | `() => Promise<Session\|null>` | Sesión actual | — |
| onAuthStateChange | `(cb) => Unsubscribe` | Reacciona a login/logout/expiración | — |

## ZoneService (RF-D)

| Método | Firma | Propósito | Errores |
|--------|-------|-----------|---------|
| listZones | `() => Promise<Zone[]>` | Zonas del usuario (HU-D2) | NetworkError |
| getZone | `(id) => Promise<Zone>` | Detalle de zona | NotFoundError |
| createZone | `(input:{name}) => Promise<Zone>` | Crear zona (HU-D1) | ValidationError (nombre requerido) |
| updateZone | `(id,{name}) => Promise<Zone>` | Editar nombre de zona (HU-D3) | NotFoundError, ValidationError |
| deleteZone | `(id) => Promise<void>` | Eliminar zona (HU-D4) | **ConflictError si tiene plantas** (Q6=A) |

> **HU-D5 (reasignar planta a zona) — camino canónico**: la columna `plants.zone_id`
> la escribe **solo** `PlantService.updatePlant(id,{zoneId})` (única fuente de verdad).
> La pantalla "Editar zona" con `PlantCheckboxList` reasigna llamando a
> `PlantService.updatePlant` por cada planta marcada/desmarcada — **no** hay un
> `plantIds` en `ZoneService` que escriba esa columna en paralelo (evita doble escritor).

## PlantService (RF-B)

| Método | Firma | Propósito | Errores |
|--------|-------|-----------|---------|
| listPlants | `() => Promise<Plant[]>` | Listado (HU-B3) | NetworkError |
| getPlant | `(id) => Promise<Plant>` | Detalle (HU-B4) | NotFoundError |
| createPlant | `(input:{name;zoneId;species?;photoUri?}) => Promise<Plant>` | Crear (HU-B1) | ValidationError (**name y zoneId requeridos** — zona-primero) |
| updatePlant | `(id,patch) => Promise<Plant>` | Editar (HU-B5); **escritor canónico de `plants.zone_id`** → reasignación de zona (HU-D5) | NotFoundError, ValidationError |
| deletePlant | `(id) => Promise<void>` | Eliminar (HU-B6) — **cascade** sensores+lecturas (Q6=A, FK DB) | NotFoundError |
| setPhoto | `(id, localUri) => Promise<Plant>` | Guardar foto **local** (HU-B2, Q7=C) | ValidationError |
| removePhoto | `(id) => Promise<Plant>` | Quitar foto | NotFoundError |

## SensorService (RF-C)

| Método | Firma | Propósito | Errores |
|--------|-------|-----------|---------|
| listSensorsByPlant | `(plantId) => Promise<Sensor[]>` | Sensores de una planta (HU-C2) | NetworkError |
| assignSensor | `(input:{plantId;externalId}) => Promise<Sensor>` | Registrar/asignar (HU-C1); una planta admite varios | ValidationError (id requerido/duplicado) |

> `removeSensor`/`updateSensor`: **Could** (no en RF-C). Diferido; ver `stories.md` nota EPIC C.

## ReadingsService (RF-E) — usa ReadingsSource

| Método | Firma | Propósito | Errores |
|--------|-------|-----------|---------|
| getLatestReadingsByPlant | `(plantId) => Promise<Reading[]>` | Última lectura por sensor de la planta | NetworkError |
| getPlantStatus | `(plantId) => Promise<PlantStatus>` | **Agregación worst-case** (Q5=A): peor estado; valor = lectura más baja; 'no-data' si ningún sensor reportó | NetworkError |
| getLatestReadingForSensor | `(sensorId) => Promise<Reading\|null>` | Lectura de un sensor (detalle) | NetworkError |

> **Lookup planta→sensores**: los métodos por planta resuelven primero los sensores
> con `SensorService.listSensorsByPlant(plantId)` y luego llaman a
> `ReadingsSource.getLatestReadings(sensorIds)`; `getPlantStatus` pasa esos sensores +
> lecturas a `computePlantStatus`. Por eso `ReadingsService` depende de `SensorService`
> (ver matriz en `component-dependency.md`).

### ReadingsSource (interfaz — Q4=A)
| Método | Firma | Propósito |
|--------|-------|-----------|
| getLatestReadings | `(sensorIds[]) => Promise<Reading[]>` | Últimas lecturas; implementado por `SupabaseReadingsSource` (tabla `readings`) o `SimulatedReadingsSource` (dev) |

### Utilidad de agregación (cliente)
`computePlantStatus(sensors: Sensor[], latest: Reading[], threshold=30): PlantStatus`
— pura y testeable; base de HumidityBadge/PlantCard/alertas.

## AlertService (RF-F)

| Método | Firma | Propósito | Errores |
|--------|-------|-----------|---------|
| getActiveAlerts | `() => Promise<Alert[]>` | Alertas activas recientes (HU-F2), leídas de la tabla `alerts` | NetworkError |
| evaluateAndNotify | `() => Promise<void>` | Enumera plantas (`PlantService`) y sus sensores (`SensorService`), deriva cruces < umbral (HU-F1), **de-duplica** contra alertas activas persistidas (tabla `alerts`), dispara **push** (HU-F3) y **auto-resuelve** al recuperar (HU-F4) | NetworkError |
| requestPushPermission | `() => Promise<PermissionStatus>` | Priming/permiso push (Q5=B refined) | — |

> **Estado de alertas persistido**: `AlertService` lee/escribe la tabla Postgres
> `alerts` (RLS por usuario) para no re-disparar la misma alerta en cada ciclo de
> polling (HU-F1: una alerta activa por condición) y para el estado resuelto/activo
> entre sesiones. Depende de `PlantService`/`SensorService` (enumerar) y `lib/supabase`
> (persistir). Ver ADR-010 en `decisions.md`.

## Componentes UI (métodos = props/callbacks)

Los componentes de UI son presentacionales; su "interfaz" son props/callbacks ya
especificados en `interaction-spec.md` (2.5): `HumidityBadge`, `PlantCard`,
`ZoneCard`, `ZoneSelector` (con `onCreateZone`), `PhotoPicker` (local),
`FormField`, `ConfirmDialog`, `EmptyState`, `ErrorBanner`, `LoadingSkeleton`.
No se reespecifican aquí para evitar duplicación.
