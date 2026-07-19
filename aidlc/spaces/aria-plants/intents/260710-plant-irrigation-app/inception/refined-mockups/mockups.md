# Refined Mockups — Aria Plants (Android MVP)

> Etapa 2.5 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Refina los `wireframes.md` y el `user-flow.md` (1.6) a media/alta fidelidad,
> cubriendo estados, tokens y comportamiento, en cumplimiento de `stories.md`,
> `requirements.md` y `team-practices.md`. Formato: markdown + ASCII refinado
> (decisión Q7=A). Estilo: **dark-first**, minimalista, marca "Aria Plants",
> **solo Android** (una columna vertical), **i18n ES/EN** (mostrado en ES).
> Design system: **React Native Paper (MD3)** — ver `design-system-mapping.md`.
> Accesibilidad objetivo: **WCAG 2.1 AA** — ver `accessibility-checklist.md`.

## Decisiones de esta etapa (respuestas Q1–Q7)

| Q | Decisión | Efecto en el diseño |
|---|----------|---------------------|
| Q1=A | Zona-primero **estricta con creación inline** | El selector de Zona en el formulario de planta ofrece "+ Crear zona" que empuja la pantalla "Nueva zona" y regresa con la zona seleccionada. Nunca hay planta sin zona; no hay zona por defecto. |
| Q2=A | **Pantallas completas** para formularios | Crear/editar planta, crear/editar zona y asignar sensor son pantallas full-screen con botón atrás (fuera de los tabs). |
| Q3=A | **React Native Paper (MD3)** | Componentes y tokens Material Design 3 en tema oscuro. |
| Q4=A | **WCAG 2.1 AA** | Contraste ≥4.5:1, labels, foco, targets ≥48dp. |
| Q5=B | Permiso push **al primer arranque tras login** | Se añade una pantalla/hoja de permiso una sola vez tras el primer login. |
| Q6=A | "Sin dato" **solo si el sensor nunca reportó** | Con ≥1 lectura se muestra siempre la última conocida, sin marca de antigüedad (fiel a RF-E2). |
| Q7=A | **Wireframes ASCII refinados** + specs + tokens | Sin herramienta externa; specs en `interaction-spec.md`. |

## Convenciones de estados (aplican a toda pantalla con datos)

Cada pantalla con datos define 5 estados; se referencian por nombre en cada pantalla:
- **Loading** — skeleton (placeholders atenuados), sin spinner de pantalla completa.
- **Empty** — ilustración/ícono + texto guía + CTA primaria (crear el primer ítem).
- **Error** — banner de error con mensaje y acción **Reintentar**; no bloquea la navegación (user-flow "Error de red").
- **Success/Loaded** — contenido normal.
- **Sin dato** — SOLO para lecturas de un sensor que nunca reportó (Q6=A): muestra "Sin dato" en lugar del %; nunca por antigüedad.

Tokens usados abajo (`color.*`, `space.*`, `radius.*`, `type.*`) se definen en `design-system-mapping.md`.

## Regla de agregación multi-sensor (planta con varios sensores)

Una planta admite **varios** sensores (HU-C1), pero el Dashboard y la lista de
plantas muestran **un** valor/estado por planta. Regla de diseño (working
assumption de esta etapa):

- **Estado de la planta** = **el peor** de los estados de sus sensores (si algún
  sensor está BAJA, la planta se muestra BAJA). Objetivo de producto: avisar si
  ALGUNA parte de la planta se seca.
- **Valor de humedad mostrado** = la lectura del sensor que dirige ese estado
  (el más bajo cuando hay BAJA; el más bajo entre los OK cuando todos están OK).
- **Alerta (HU-F1)** = se dispara a nivel de planta cuando **cualquiera** de sus
  sensores cruza por debajo del umbral; `AlertItem` identifica la planta (y el
  sensor disparador cuando aplique).
- **Sin dato**: si TODOS los sensores de la planta nunca reportaron → "Sin dato"
  (Q6=A). Si al menos uno reportó, se usa ese/esos para la agregación.

> **OQ-refined-1 (abierta)**: esta regla worst-case es la intención de diseño;
> `requirements.md` RF-F1 dice "esa planta/sensor" de forma ambigua. **Application
> Design debe confirmar o ajustar** la agregación y el disparo de alerta antes de
> Code Generation (paralelo a OQ-3/C-4). El detalle de planta (§5) sí muestra la
> lectura **por sensor**, así que el dato crudo por sensor nunca se pierde.

---

## 1. Login (auth, fuera de tabs)

Refina `wireframes.md` §1. Cubre HU-A2 (login) y enlace a HU-A1 (registro).

```
+------------------------------+
|                              |
|         (o) Aria Plants      |   <- logo hoja + wordmark (type.display)
|                              |
|  Correo                      |   <- label (type.label)
|  [__________________________]|   <- TextInput (outlined)
|                              |
|  Contrasena           (ojo)  |   <- toggle mostrar/ocultar
|  [__________________________]|
|                              |
|  [       Iniciar sesion     ]|   <- Button contained (color.primary)
|                              |
|  Crear cuenta                |   <- Button text -> Registro (HU-A1)
|                              |
|  Idioma:  (o) ES   ( ) EN    |   <- SegmentedButtons (i18n, HU-X1)
+------------------------------+
```
<!-- Text fallback: pantalla de login con logo y wordmark, campo Correo, campo Contraseña con toggle de visibilidad, botón primario Iniciar sesión, botón de texto Crear cuenta y selector de idioma ES/EN. -->

- **Estados**: Loading (botón con spinner al enviar, campos deshabilitados) · Error (`color.error` inline: "Credenciales incorrectas" — RF-A2) · Success (navega a Dashboard).
- **Validación**: correo con formato válido y contraseña no vacía antes de habilitar el botón.
- **Traza**: HU-A2, HU-A1 (enlace), HU-X1 (idioma). A11y: foco inicial en Correo; ver checklist §Login.

## 2. Registro (auth, fuera de tabs)

Refina `wireframes.md` §2. Cubre HU-A1.

```
+------------------------------+
|  <  Crear cuenta             |   <- Appbar con back
|                              |
|  Nombre                      |
|  [__________________________]|
|  Correo                      |
|  [__________________________]|
|  Contrasena           (ojo)  |
|  [__________________________]|
|  Repetir contrasena   (ojo)  |
|  [__________________________]|
|  * Min. segun politica       |   <- helper text (S-5: política Supabase)
|                              |
|  [       Crear cuenta       ]|
|  Ya tengo cuenta -> Entrar   |
+------------------------------+
```
<!-- Text fallback: formulario de registro con Nombre, Correo, Contraseña y su repetición (con helper de política), botón Crear cuenta y enlace a login. -->

- **Estados**: Loading (envío) · Error inline por campo (correo ya registrado → RF-A1; contraseñas no coinciden; política no cumplida → S-5) · Success (queda autenticado, va a permiso push §9 y luego Dashboard).
- **Traza**: HU-A1. A11y: validación inline por campo, foco al primer error.

## 3. Dashboard (tab: Inicio)

Refina `wireframes.md` §3. Cubre HU-E1, HU-E2, HU-F2 (alertas in-app), HU-A4 (solo mis datos).

```
+------------------------------+
|  Aria Plants                 |   <- Appbar (type.title)
|                              |
|  Resumen                     |   <- section (type.titleSmall)
|  +------------+ +----------+ |
|  | Plantas  6 | | Zonas  1 | |   <- Cards de conteo
|  +------------+ +----------+ |
|                              |
|  Alertas recientes           |
|  +--------------------------+|
|  |(!) Menta  humedad baja   ||   <- ícono + texto (no solo color)
|  |    hace 5 min            ||
|  |(!) Ficus  humedad baja   ||
|  +--------------------------+|
|                              |
|  Plantas (humedad)           |
|  +--------------------------+|
|  | Albahaca     62%  [ OK ] ||   <- HumidityBadge (chip)
|  | Menta        18%  [BAJA] ||
|  | Ficus        21%  [BAJA] ||
|  +--------------------------+|
+------------------------------+
| Inicio | Plantas | Zonas |Perfil|  <- BottomNavigation (4 tabs)
+------------------------------+
```
<!-- Text fallback: dashboard con tarjetas de conteo (plantas, zonas), lista de alertas recientes con icono, planta y antigüedad, y lista de plantas con su humedad y chip de estado OK/BAJA. Barra inferior con 4 tabs. -->

- **Estados**:
  - Loading: skeletons en las 3 secciones.
  - Empty (cuenta nueva sin plantas): Resumen en 0; "Alertas recientes" muestra "Sin alertas recientes" (HU-F2 empty); bloque Plantas con estado vacío + CTA "Crear tu primera planta".
  - Error (polling/Supabase falla): banner "No se pudo actualizar" + Reintentar; se conservan los últimos datos visibles.
  - Sin dato: una planta cuyo(s) sensor(es) nunca reportaron muestra "Sin dato" en vez de % (Q6=A).
  - Alerta resuelta (HU-F4): cuando la humedad se recupera, la alerta se marca **resuelta** y **sale** de "Alertas recientes" (la lista solo muestra alertas activas); la planta vuelve a estado OK en la lista. No hay descarte manual (RF-F3).
- **Ejemplo con alerta resuelta** (Ficus se recuperó a 34%):

```
|  Alertas recientes           |
|  +--------------------------+|
|  |(!) Menta  humedad baja   ||   <- activa
|  |    hace 5 min            ||
|  +--------------------------+|   <- Ficus ya NO aparece (resuelta)
```
<!-- Text fallback: al recuperarse la humedad de Ficus, su alerta se resuelve sola y desaparece de "Alertas recientes"; solo Menta (aún baja) permanece en la lista. -->

- **Polling (HU-E2)**: refresco automático cada ~5 min + pull-to-refresh manual; la humedad mostrada = última lectura (NFR-Frescura).
- **Traza**: HU-E1, HU-E2, HU-F2, HU-A4, RF-E3 (solo humedad). A11y: h1 marca, h2 por sección, alertas con ícono+texto.

## 4. Plantas — Lista (tab: Plantas)

Refina `wireframes.md` §4. Cubre HU-B3.

```
+------------------------------+
|  Plantas              [ + ]  |   <- Appbar action "Agregar planta" (FAB alterno)
|                              |
|  +--------------------------+|
|  | Albahaca                 ||
|  | Zona: Balcon  62% [ OK ] ||   <- PlantCard -> detalle
|  +--------------------------+|
|  | Menta                    ||
|  | Zona: Balcon  18% [BAJA] ||
|  +--------------------------+|
|  | Ficus                    ||
|  | Zona: Balcon  21% [BAJA] ||
|  +--------------------------+|
+------------------------------+
| Inicio | Plantas | Zonas |Perfil|
+------------------------------+
```
<!-- Text fallback: lista de plantas con acción + para agregar; cada tarjeta muestra nombre, zona, humedad y chip de estado, y abre el detalle. -->

- **Estados**: Loading (skeleton de tarjetas) · Empty ("Aún no tienes plantas" + CTA "Crear planta") · Error (banner + Reintentar) · Sin dato (chip "Sin dato").
- **Regla zona-primero (Q1=A)**: si el usuario toca **+** y NO existe ninguna zona, el formulario de planta (§6) resolverá la creación de zona inline; nunca se crea planta sin zona.
- **Traza**: HU-B3. A11y: cada tarjeta es un enlace etiquetado con nombre+estado.

## 5. Planta — Detalle (fuera de tabs)

Refina `wireframes.md` §5. Cubre HU-B4, HU-C2, HU-E2, enlace a HU-B5/HU-B6/HU-C1.

```
+------------------------------+
|  <  Menta            [Editar]|   <- Appbar back + acción Editar (HU-B5)
|                              |
|      +------------------+    |
|      |  (foto o marcador)|   |   <- foto opcional (HU-B2); marcador si no hay
|      +------------------+    |
|  Humedad actual              |
|      +------------------+    |
|      |     18%   [BAJA] |    |   <- HumidityBadge grande
|      +------------------+    |
|  Especie: Hierbabuena        |   <- si existe
|  Zona de riego: Balcon       |   <- HU-D2 (una zona)
|                              |
|  Sensores                    |   <- HU-C2
|  +--------------------------+|
|  | HUM-01 (humedad)  18%    ||   <- última lectura por sensor
|  | HUM-02 (humedad) Sin dato||   <- sensor que nunca reportó (Q6=A)
|  +--------------------------+|
|  [ + Asignar sensor ]        |   <- HU-C1 (push full-screen §6b)
|                              |
|  [   Eliminar planta   ]     |   <- HU-B6 (confirmación)
+------------------------------+
```
<!-- Text fallback: detalle de planta con foto opcional, humedad actual con estado, especie y zona, lista de sensores con su última lectura (o "Sin dato" si nunca reportó), botón asignar sensor y botón eliminar planta con confirmación. -->

- **Estados**: Loading (skeleton) · Error (banner + Reintentar) · Sin dato (por sensor).
- **Eliminar (HU-B6)**: diálogo de confirmación; el efecto sobre sensores queda pendiente (OQ-3, a fijar en Application Design) — el diálogo advierte "se quitarán/eliminarán sus sensores" según lo que se decida.
- **Traza**: HU-B4, HU-C2, HU-B2, HU-D2, HU-E2, HU-B5 (Editar), HU-B6, HU-C1.

## 6. Crear / Editar planta (fuera de tabs)

Refina `wireframes.md` §6. Cubre HU-B1, HU-B2, HU-B5, HU-D2, y el enforcement zona-primero (Q1=A).

```
+------------------------------+
|  <  Nueva planta             |   <- "Editar planta" en modo edición
|                              |
|  Nombre *                    |
|  [__________________________]|   <- obligatorio (RF-B1)
|  Especie / tipo              |
|  [__________________________]|   <- opcional (texto libre)
|                              |
|  Foto (opcional)             |   <- HU-B2 (opcional al crear, Q5 producto)
|  [ + Anadir foto ]           |
|                              |
|  Zona de riego *             |   <- OBLIGATORIO (zona-primero)
|  [ Selecciona una zona    v ]|   <- ZoneSelector
|     -> si no hay zonas:      |
|     [ + Crear zona ]         |   <- push a §7b, regresa con la zona elegida
|                              |
|  Umbral de humedad: 30% fijo |   <- informativo (RF-F1, no editable)
|                              |
|  [        Guardar          ] |   <- deshabilitado sin Nombre y sin Zona
+------------------------------+
```
<!-- Text fallback: formulario de planta con Nombre obligatorio, Especie opcional, Foto opcional, selector de Zona obligatorio (que ofrece crear zona si no existe ninguna), umbral fijo informativo y botón Guardar deshabilitado hasta que haya Nombre y Zona. -->

- **Enforcement zona-primero (Q1=A)**: el botón **Guardar** permanece deshabilitado hasta que haya Nombre y una Zona seleccionada. Si no existen zonas, el único camino del selector es "+ Crear zona" (push §7b). No hay zona por defecto.
- **Foto (HU-B2)**: "+ Añadir foto" usa el `PhotoPicker` (cámara o galería). Es
  **opcional al crear** (Q5 producto). Requiere un **permiso de Android** (cámara/
  galería): se pide **en contexto**, solo al tocar "+ Añadir foto" (con explicación
  previa); si se deniega, la planta se crea/edita igual, sin foto, y se muestra el
  marcador por defecto. *(Mecanismo/librería concreta y almacenamiento: diferido a
  Application/NFR Design — OQ-refined-2 y OQ-2.)* Ver spec del componente en
  `interaction-spec.md` → `PhotoPicker`.
- **Estados**: Loading (guardando) · Error (validación inline: Nombre requerido; Zona requerida) · Success (va al Detalle §5).
- **Traza**: HU-B1, HU-B2, HU-B5 (edición), HU-D2. A11y: labels asociados, foco inicial en Nombre.

## 6b. Asignar sensor (fuera de tabs)

Nueva pantalla derivada de HU-C1 (los rough mockups lo insinuaban en el detalle).

```
+------------------------------+
|  <  Asignar sensor           |
|                              |
|  Planta: Menta               |
|                              |
|  Identificador del sensor *  |
|  [__________________________]|   <- id/nombre del sensor (contrato C-4 por definir)
|  Tipo: Humedad (fijo MVP)    |   <- RF-E3 (solo humedad)
|                              |
|  [        Asignar          ] |
+------------------------------+
```
<!-- Text fallback: pantalla para asignar un sensor a una planta, con identificador del sensor obligatorio, tipo humedad fijo, y botón Asignar. Una planta admite varios sensores. -->

- **Nota (C-4)**: el contrato real de sensores está por definir; la app aísla el origen tras un adaptador (S-2). Esta pantalla registra/asocia el sensor; la ingesta de lecturas es externa (C-2).
- **Estados**: Loading · Error (id vacío/duplicado) · Success (vuelve al Detalle con el sensor listado).
- **Traza**: HU-C1.

## 7. Zonas — Lista (tab: Zonas)

Refina `wireframes.md` §7. Cubre HU-D2.

```
+------------------------------+
|  Zonas de riego       [ + ]  |
|                              |
|  +--------------------------+|
|  | Balcon                   ||
|  | 6 plantas   1 en alerta  ||   <- ZoneCard -> Editar zona
|  +--------------------------+|
+------------------------------+
| Inicio | Plantas | Zonas |Perfil|
+------------------------------+
```
<!-- Text fallback: lista de zonas con acción + para crear; cada tarjeta muestra nombre, número de plantas y cuántas en alerta, y abre editar zona. -->

- **Estados**: Loading (skeleton) · Empty ("Crea una zona para agrupar tus plantas" + CTA) · Error (banner + Reintentar).
- **Traza**: HU-D2, HU-D1 (+).

## 7b. Crear / Editar zona (fuera de tabs)

Refina `wireframes.md` §7b. Cubre HU-D1, HU-D3, HU-D4, HU-D5 (reasignación por checkboxes).

```
+------------------------------+
|  <  Nueva zona               |   <- "Editar zona" en edición
|                              |
|  Nombre de la zona *         |
|  [__________________________]|
|                              |
|  Plantas en esta zona        |   <- HU-D5 (asignar/reasignar)
|  +--------------------------+|
|  | [x] Albahaca             ||
|  | [x] Menta                ||
|  | [ ] Ficus                ||
|  +--------------------------+|
|                              |
|  [        Guardar          ] |
|  [    Eliminar zona    ]     |   <- solo en edición (HU-D4)
+------------------------------+
```
<!-- Text fallback: formulario de zona con Nombre obligatorio y lista de plantas con checkboxes para asignarlas; botón Guardar y, en edición, Eliminar zona. -->

- **Llamada desde el flujo de planta (Q1=A)**: cuando se abre como "+ Crear zona" desde el selector de planta, al Guardar regresa a §6 con la nueva zona ya seleccionada.
- **Eliminar zona (HU-D4)**: si la zona tiene plantas, el diálogo impide el borrado y explica que hay que mover/eliminar sus plantas primero (regla exacta → Application Design, OQ-3).
- **Estados**: Loading · Error (Nombre requerido) · Success.
- **Traza**: HU-D1, HU-D3, HU-D4, HU-D5.

## 8. Perfil / Ajustes (tab: Perfil)

Refina `wireframes.md` §8. Cubre HU-A3, HU-X1, toggle push (HU-F3), umbral informativo.

```
+------------------------------+
|  Perfil                      |
|                              |
|  (o) Practicante Okuo        |   <- avatar + nombre
|      correo@ejemplo.com      |
|                              |
|  Ajustes                     |
|  Idioma        (o)ES ( )EN   |   <- HU-X1 (activo)
|  Tema          Oscuro        |   <- fijo MVP (claro: pronto)
|  Notificaciones push   [x]   |   <- toggle (HU-F3)
|  Umbral humedad   30% (fijo) |   <- informativo (RF-F1)
|                              |
|  [     Cerrar sesion     ]   |   <- HU-A3 (confirmación)
+------------------------------+
| Inicio | Plantas | Zonas |Perfil|
+------------------------------+
```
<!-- Text fallback: perfil con datos de cuenta y ajustes: idioma ES/EN activo, tema oscuro fijo (claro post-MVP), toggle de notificaciones push, umbral de humedad informativo fijo 30%, y botón cerrar sesión con confirmación. -->

- **Toggle push (HU-F3)**: si el permiso del sistema fue denegado, el toggle muestra estado "Permiso denegado — actívalo en Ajustes de Android"; las alertas siguen en la lista in-app (degradación limpia).
- **Estados**: Loading (carga de perfil) · Error (fallo al cargar el perfil o al guardar un ajuste como idioma/toggle push → banner con Reintentar; el ajuste no se aplica hasta confirmar) · Success. Cerrar sesión → diálogo de confirmación → Login.
- **Traza**: HU-A3, HU-X1, HU-F3, RF-F1 (umbral informativo). A11y: h1 Perfil, h2 Ajustes, controles etiquetados.

## 9. Permiso de notificaciones push (una sola vez, tras primer login — Q5=B)

Nueva pantalla/hoja derivada de Q5=B + HU-F3.

```
+------------------------------+
|                              |
|   (!) Mantente al tanto      |
|                              |
|   Recibe un aviso cuando una |
|   planta necesite agua       |
|   (humedad por debajo del    |
|   umbral).                   |
|                              |
|  [   Activar notificaciones ]|   <- dispara el permiso nativo de Android
|  [   Ahora no               ]|   <- continúa sin push (queda in-app)
+------------------------------+
```
<!-- Text fallback: pantalla de priming (una vez, tras el primer login) que explica el beneficio de las notificaciones y ofrece Activar notificaciones (dispara el permiso del sistema) o Ahora no. -->

- **Frecuencia**: se muestra **una sola vez** tras el primer login (Q5=B). "Ahora no" no bloquea nada; el usuario puede activar luego desde Perfil.
- **Traza**: HU-F3, RF-F2 (push). *(Mecanismo técnico y permisos: OQ-4 / S-3 — a detallar en Application/NFR Design.)*

---

## Mapa de navegación (refinado)

```
[Login] --crear cuenta--> [Registro]
   |  (auth ok)               |
   +-----------+--------------+
               v
     [Permiso push] (1a vez, Q5=B)
               v
   +------ Tabs persistentes -------------------+
   | [Inicio/Dashboard] [Plantas] [Zonas] [Perfil]
   +-------------------------------------------+
        |            |          |
        v            v          v
   (detalle)   [Planta detalle] [Zona editar]
                    |   \            ^
                    v    \           |
             [Crear/Editar planta]---+  (+ Crear zona inline, Q1=A)
                    |
                    v
             [Asignar sensor]
```
<!-- Text fallback: tras autenticar y (una vez) el priming de push, se entra a los 4 tabs. Plantas abre detalle y crear/editar planta; el formulario de planta puede crear una zona inline (push a editar zona y regreso). Detalle abre asignar sensor. Zonas abre crear/editar zona. -->

## Trazabilidad pantalla -> historias

| Pantalla | Historias / Requisitos |
|----------|------------------------|
| 1 Login | HU-A2, HU-X1 |
| 2 Registro | HU-A1 |
| 3 Dashboard | HU-E1, HU-E2, HU-F2, HU-A4, RF-E3 |
| 4 Plantas lista | HU-B3 |
| 5 Planta detalle | HU-B4, HU-C2, HU-B2, HU-D2, HU-E2, HU-B5, HU-B6, HU-C1 |
| 6 Crear/editar planta | HU-B1, HU-B2, HU-B5, HU-D2 (zona-primero Q1=A) |
| 6b Asignar sensor | HU-C1 |
| 7 Zonas lista | HU-D2 |
| 7b Crear/editar zona | HU-D1, HU-D3, HU-D4, HU-D5 |
| 8 Perfil | HU-A3, HU-X1, HU-F3, RF-F1 |
| 9 Permiso push | HU-F3, RF-F2 (Q5=B) |

Cobertura: todas las historias con superficie de pantalla del MVP tienen una
pantalla asociada; las historias transversales (HU-A4 aislamiento, HU-X1 idioma,
NFR-Frescura) se reflejan como comportamiento/ajuste en las pantallas anteriores.
