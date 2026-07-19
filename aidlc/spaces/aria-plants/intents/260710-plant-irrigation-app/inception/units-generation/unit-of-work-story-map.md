# Unit ↔ Story Map — Aria Plants (Android MVP)

> Etapa 2.7 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Mapea cada historia de `stories.md` a su(s) unidad(es) de `unit-of-work.md`,
> según el diseño en `application-design/` — `components.md`, `component-methods.md`,
> `services.md`, `component-dependency.md`, `decisions.md` — y `requirements.md`.
> Verifica cobertura: toda historia asignada, toda unidad con historias.

## Historias por unidad

### U1 · foundation
- **HU-X1** (idioma ES/EN — infraestructura i18n) *(cross-cutting con auth, profile)*
- **HU-A4** (aislamiento — cliente con solo anon key + reset de caché de sesión) *(cross-cutting con auth)*
- Soporta además: dark-first (NFR-Usabilidad), umbral 30% en config (RF-F1), polling (RF-E2) — infraestructura consumida por otras unidades.
- **Orden interno**: primero cliente Supabase + config + i18n + tema, luego navegación y primitivos UI.

### U2 · auth
- **HU-A1** Registrar cuenta → **HU-A2** Iniciar sesión → **HU-A4** Aislamiento (sesión/RLS) → **HU-A3** Cerrar sesión *(A3 cross-cutting con profile)*.
- **Orden interno**: registro/login primero (habilitan todo), luego logout y priming de push.

### U3 · zones
- **HU-D1** Crear zona → **HU-D2** Ver zonas → **HU-D3** Editar zona → **HU-D4** Eliminar zona.
- (**HU-D5** reasignar planta a zona vive en `plants`, ver abajo — escritor canónico.)
- **Orden interno**: crear+ver primero (habilitan zona-primero), luego editar/eliminar.

### U4 · plants
- **HU-B1** Crear planta (zona-primero) → **HU-B3** Ver listado → **HU-B4** Ver detalle → **HU-B2** Foto (local) → **HU-B5** Editar → **HU-B6** Eliminar → **HU-D5** Reasignar planta a otra zona (escritor canónico de `plants.zone_id`).
- **Orden interno**: crear/listar/detalle primero, luego foto y editar/eliminar/reasignar.

### U5 · sensors
- **HU-C1** Registrar/asignar sensor → **HU-C2** Ver sensores + última lectura.
- **Orden interno**: asignar primero, luego la vista con lecturas.

### U6 · dashboard
- **HU-E1** Dashboard de humedad → **HU-E2** Actualización por polling.
- **Orden interno**: lectura+agregación (ReadingsService/ReadingsSource/computePlantStatus) primero, luego el polling automático.

### U7 · alerts
- **HU-F1** Generar alerta (umbral 30%) → **HU-F2** Ver alertas recientes in-app → **HU-F4** Auto-resolución → **HU-F3** Push *(F3 cross-cutting con profile: el toggle)*.
- **Orden interno**: derivación+persistencia (tabla `alerts`) primero, luego lista in-app, auto-resolución, y por último push.

### U8 · profile
- **HU-A3** Cerrar sesión (botón) *(servicio en auth)* · **HU-X1** Selector de idioma (UI) · **HU-F3** Toggle de push (UI).
- **Orden interno**: pantalla de ajustes con idioma/tema/umbral, luego toggle push y logout.

## Historias cross-cutting (abarcan varias unidades)

| Historia | Unidades | Reparto |
|----------|----------|---------|
| **HU-X1** (idioma ES/EN) | foundation (infra i18n) + auth (selector en Login) + profile (selector en Ajustes) | infra en foundation; superficies en auth/profile |
| **HU-A4** (aislamiento) | foundation (anon key + reset caché) + auth (sesión) + DB (RLS) | cliente en foundation/auth; RLS en Supabase |
| **HU-A3** (cerrar sesión) | auth (AuthService.signOut) + profile (botón) | servicio en auth; entrada de UI en profile |
| **HU-F3** (push) | alerts (entrega/registro) + profile (toggle on/off) | entrega en alerts; control en profile. La **preferencia del toggle** se guarda en `config`/almacenamiento local de `foundation` (del que dependen tanto `alerts` como `profile`), así que no requiere una arista nueva ni una tabla en Supabase. |

## Verificación de cobertura

**Todas las historias asignadas (24 formales):**
- Auth: A1, A2, A3, A4 ✔
- Plants: B1, B2, B3, B4, B5, B6, D5 ✔
- Sensors: C1, C2 ✔
- Zones: D1, D2, D3, D4 ✔
- Dashboard: E1, E2 ✔
- Alerts: F1, F2, F3, F4 ✔
- Transversal: X1 ✔

**Toda unidad tiene historias:**
- foundation (X1, A4 + infra), auth (A1–A4), zones (D1–D4), plants (B1–B6, D5), sensors (C1–C2), dashboard (E1–E2), alerts (F1–F4), profile (A3, X1, F3) ✔

Sin historias huérfanas y sin unidades vacías. El **walking skeleton** (login → crear zona → crear planta → asignar sensor → ver humedad) toca auth+zones+plants+sensors+dashboard sobre foundation; su composición como Bolt 1 la define Delivery Planning (2.8).
