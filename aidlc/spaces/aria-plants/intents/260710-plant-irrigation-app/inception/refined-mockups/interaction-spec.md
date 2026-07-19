# Interaction Specification — Aria Plants (Android MVP)

> Etapa 2.5 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Especificación de componentes e interacciones para las pantallas de
> `mockups.md`. Formato de componente adaptado de
> `.claude/knowledge/aidlc-design-agent/component-spec-template.md` a **móvil táctil**
> (sin hover; "breakpoints" = clases de ancho de teléfono Android; foco = TalkBack /
> teclado externo). Base: **React Native Paper (MD3)** (`design-system-mapping.md`).
> Cumple `stories.md`, `requirements.md`, `wireframes.md`, `user-flow.md` y
> `team-practices.md` (stack Expo/RN + TS strict; sin strings hardcodeados).

## Patrones de interacción globales

- **Navegación**: 4 tabs persistentes (Inicio/Plantas/Zonas/Perfil). Auth y
  formularios crear/editar son **pantallas completas** fuera de los tabs, con
  botón atrás (Q2=A). Sesión expirada → Login conservando destino (user-flow).
- **Formularios**: validación **inline por campo**; botón primario deshabilitado
  hasta cumplir requisitos mínimos; al enviar, el botón muestra spinner y los
  campos se deshabilitan (Loading). Errores del servidor se muestran junto al
  campo o en banner según el caso.
- **Zona-primero (Q1=A)**: el `ZoneSelector` del formulario de planta nunca permite
  guardar sin zona; si no hay zonas, su única acción es "+ Crear zona" (push a
  Crear zona y regreso con la zona seleccionada).
- **Refresco de datos (HU-E2)**: polling automático ~5 min + pull-to-refresh; la
  UI siempre muestra la última lectura conocida (NFR-Frescura); nunca bloquea por
  esperar datos.
- **Acciones destructivas**: eliminar planta/zona y cerrar sesión → `Dialog` de
  confirmación (Portal), foco inicial en la acción segura (Cancelar).
- **Estados**: cada lista/lectura maneja Loading (skeleton) / Empty (CTA) / Error
  (Reintentar) / Sin dato (solo si el sensor nunca reportó, Q6=A).
- **i18n**: todo label proviene de i18n (ES por defecto, EN por selector). Sin
  strings hardcodeados.

---

## FormField (campo de texto)

| Field | Value |
|---|---|
| Component | FormField |
| Description | Campo de texto etiquetado con validación inline | 
| Category | input |
| RN Paper | `TextInput` (outlined) + `HelperText` |

### States

| State | Description | Trigger |
|---|---|---|
| default | Render inicial, vacío o con valor | carga de pantalla |
| focus | Campo enfocado (teclado/TalkBack) | tap / Tab |
| filled | Con valor válido | entrada del usuario |
| error | Valor inválido o requerido vacío | validación / submit |
| disabled | No editable (envío en curso) | Loading |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| label | string (i18n) | yes | — | Etiqueta visible y accesible |
| value | string | yes | "" | Valor controlado |
| required | boolean | no | false | Muestra `*` y valida no-vacío |
| secureTextEntry | boolean | no | false | Contraseña (con toggle ojo) |
| error | string (i18n) | no | — | Mensaje de error inline |
| helper | string (i18n) | no | — | Texto de ayuda (p. ej. política Supabase) |

### Responsive (Android)

| Clase | Comportamiento |
|---|---|
| phone-sm (<360dp) | Padding `space.sm`; label sobre el campo |
| phone-md (360–420dp) | Layout por defecto |
| phone-lg (>420dp) | Igual; ancho máximo del contenido centrado |

### Accessibility

| Requisito | Implementación |
|---|---|
| Rol | textbox (nativo TextInput) |
| Interacción | tap para enfocar; teclado apropiado (email/número) |
| Label | `label` asociado (no placeholder-only) |
| Contraste | AA (texto 4.5:1, borde 3:1) |
| Lector | anuncia label, estado required y error |
| Foco | al primer campo con error tras submit |

Traza: HU-A1, HU-A2, HU-B1, HU-D1.

---

## HumidityBadge (indicador de humedad/estado)

| Field | Value |
|---|---|
| Component | HumidityBadge |
| Description | Muestra % de humedad y estado (OK / BAJA / Sin dato) | 
| Category | display |
| RN Paper | `Chip` (color por estado) |

### States

| State | Description | Trigger |
|---|---|---|
| ok | Humedad ≥ 30% | lectura ≥ umbral (RF-F1) |
| low | Humedad < 30% | lectura < umbral |
| no-data | Sensor sin ninguna lectura | 0 lecturas (Q6=A) |
| loading | Cargando lectura | fetch en curso |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| value | number \| null | yes | — | % humedad; null = sin dato |
| status | "ok" \| "low" \| "no-data" | yes | — | Estado ya derivado por el llamador |
| size | "sm" \| "lg" | no | "sm" | lg en detalle de planta |
| level | "plant" \| "sensor" | no | "plant" | Si es de planta (agregado) o de un sensor concreto |

### Derivación (planta con varios sensores)

`HumidityBadge` es **presentacional**: recibe `value`/`status` ya calculados. Cuando
`level="plant"`, el llamador calcula el agregado según la **regla worst-case** de
`mockups.md` → "Regla de agregación multi-sensor" (status = peor de sus sensores;
value = lectura que dirige ese status; "no-data" solo si TODOS los sensores nunca
reportaron). Cuando `level="sensor"` (detalle de planta), muestra la lectura de ese
sensor tal cual. *(La regla worst-case es intención de diseño; Application Design la
confirma — OQ-refined-1.)*

### Responsive (Android)

| Clase | Comportamiento |
|---|---|
| phone-sm (<360dp) | Chip compacto; el nombre de la planta trunca con elipsis para no empujar el chip fuera de pantalla |
| phone-md/-lg | Layout por defecto |

### Accessibility

| Requisito | Implementación |
|---|---|
| No solo color | Ícono + texto ("OK"/"BAJA"/"Sin dato") además del color |
| Contraste | AA sobre surface |
| Lector | anuncia "humedad 18 por ciento, estado bajo" |

Traza: HU-E1, HU-E2, HU-C2, HU-F1, RF-F1, Q6=A.

---

## PlantCard

| Field | Value |
|---|---|
| Component | PlantCard |
| Description | Tarjeta de planta en listas; navega al detalle | 
| Category | navigation/display |
| RN Paper | `Card` + `TouchableRipple` |

### States

| State | Description | Trigger |
|---|---|---|
| default | Nombre + zona + HumidityBadge | carga |
| pressed | Ripple al tocar | tap |
| loading | Skeleton | fetch |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| name | string | yes | — | Nombre de la planta |
| zoneName | string | yes | — | Zona (una, HU-D2) |
| humidity | number \| null | yes | — | Humedad **a nivel de planta** (agregada, ver regla worst-case) |
| status | "ok"\|"low"\|"no-data" | yes | — | Estado agregado de la planta |
| onPress | () => void | yes | — | Abre detalle |

> `humidity`/`status` son el **agregado de la planta** (regla worst-case de
> `mockups.md`); la tarjeta no muestra sensores individuales — eso vive en el
> detalle (§5). Usa `HumidityBadge level="plant"`.

### Responsive (Android)

| Clase | Comportamiento |
|---|---|
| phone-sm (<360dp) | Nombre en una línea con elipsis; zona debajo; badge alineado a la derecha sin solaparse |
| phone-md/-lg | Nombre + zona + badge en el layout por defecto |

### Accessibility

| Requisito | Implementación |
|---|---|
| Rol | button/link (toda la tarjeta tocable) |
| Label | "Menta, zona Balcón, humedad 18%, estado bajo" |
| Touch target | ≥48dp de alto |
| Foco | orden lógico en la lista |

Traza: HU-B3, HU-B4.

---

## ZoneSelector (con creación inline) — componente clave zona-primero

| Field | Value |
|---|---|
| Component | ZoneSelector |
| Description | Selecciona una zona; si no hay zonas, solo permite crear una | 
| Category | input |
| RN Paper | `Menu` / `List.Accordion` + item CTA "+ Crear zona" |

### States

| State | Description | Trigger |
|---|---|---|
| empty-no-zones | No existen zonas: única opción "+ Crear zona" | cuenta sin zonas |
| closed | Zona seleccionada mostrada | selección |
| open | Lista de zonas + "+ Crear zona" | tap |
| error | Fallback defensivo: requerido sin zona | re-validación (ver nota) |

> El estado `error` es un **fallback defensivo** (re-validación en submit y/o
> validación de servidor): con la UI tal cual, Guardar está deshabilitado sin zona,
> así que el usuario no debería alcanzarlo por flujo normal. Se mantiene por
> robustez (p. ej. estado inconsistente o validación en backend), no como camino esperado.

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| zones | Zone[] | yes | [] | Zonas del usuario |
| selectedId | string \| null | yes | null | Zona elegida |
| onSelect | (id) => void | yes | — | Selección |
| onCreateZone | () => void | yes | — | Push a "Nueva zona"; al volver, selecciona la creada |

### Behaviour (Q1=A)

- Si `zones.length === 0`: el selector muestra solo "+ Crear zona"; **Guardar del
  formulario de planta permanece deshabilitado** hasta que exista y se elija una zona.
- "+ Crear zona" empuja la pantalla Crear zona (§7b); al Guardar allí, regresa y
  auto-selecciona la nueva zona. No hay zona por defecto implícita.

### Accessibility

| Requisito | Implementación |
|---|---|
| Rol | combobox/menú |
| Interacción | tap abre; selección cierra |
| Label | "Zona de riego, requerido" |
| Lector | anuncia opción seleccionada y la acción crear |

Traza: HU-B1, HU-D1, HU-D2, regla `Decided` zona-primero (cid user-stories:c5).

---

## AlertItem (alerta reciente)

| Field | Value |
|---|---|
| Component | AlertItem |
| Description | Fila de alerta de humedad baja en el Dashboard | 
| Category | feedback |
| RN Paper | `List.Item` con ícono izquierdo |

### States

| State | Description | Trigger |
|---|---|---|
| active | Alerta vigente (planta aún BAJA) | humedad < umbral |
| resolved | Auto-resuelta (humedad recuperada) | humedad ≥ umbral (HU-F4) |

> **Comportamiento en el Dashboard**: "Alertas recientes" muestra solo alertas
> **activas**. Al resolverse (HU-F4), la fila **sale** de la lista sin acción del
> usuario (RF-F3, sin descarte manual) — ver `mockups.md` §3 ("Ejemplo con alerta
> resuelta"). El estado `resolved` existe para la transición/animación de salida y
> para el registro; no permanece listado.

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| plantName | string | yes | — | Planta afectada |
| sensorName | string | no | — | Sensor disparador (cuando aplique, agregación multi-sensor) |
| firedAt | string | yes | — | Momento ("hace 5 min") |
| resolved | boolean | no | false | Marca resuelta (transición de salida) |

### Accessibility

| Requisito | Implementación |
|---|---|
| No solo color | Ícono (!) + texto "humedad baja" |
| Lector | "Alerta, Menta, humedad baja, hace 5 minutos" |
| Salida | al resolverse, se anuncia "alerta resuelta" antes de retirar la fila (no mover el foco bruscamente) |

Traza: HU-F1, HU-F2, HU-F4.

---

## PushToggle

| Field | Value |
|---|---|
| Component | PushToggle |
| Description | Activa/desactiva notificaciones push desde Perfil | 
| Category | input |
| RN Paper | `Switch` en `List.Item` |

### States

| State | Description | Trigger |
|---|---|---|
| on | Push activo y permiso concedido | permiso OK |
| off | Push desactivado por el usuario | toggle off |
| denied | Permiso del sistema denegado | permiso denegado |

### Behaviour

- Si `denied`: el toggle queda off con texto "Permiso denegado — actívalo en
  Ajustes de Android"; las alertas siguen visibles in-app (degradación limpia, HU-F3).
- El **priming** de permiso (§9 de mockups) ocurre una vez tras el primer login (Q5=B),
  no aquí; aquí solo se re-activa/desactiva.

### Accessibility

| Requisito | Implementación |
|---|---|
| Rol | switch |
| Label | "Notificaciones push" + estado |
| Lector | anuncia on/off/denegado |

Traza: HU-F3, RF-F2.

---

## ConfirmDialog (acciones destructivas)

| Field | Value |
|---|---|
| Component | ConfirmDialog |
| Description | Confirma eliminar planta/zona o cerrar sesión | 
| Category | feedback |
| RN Paper | `Portal` + `Dialog` |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| title | string (i18n) | yes | — | p. ej. "Eliminar planta" |
| message | string (i18n) | yes | — | Consecuencia (incl. sensores, OQ-3) |
| confirmLabel | string (i18n) | yes | — | "Eliminar" |
| blocked | boolean | no | false | Zona con plantas: impide y explica (HU-D4) |

### Accessibility

| Requisito | Implementación |
|---|---|
| Rol | dialog (modal) |
| Foco | inicial en Cancelar (acción segura); trap dentro del diálogo |
| Dismiss | back/afuera cancela |

Traza: HU-B6, HU-D4, HU-A3.

---

## PhotoPicker (foto opcional de planta)

| Field | Value |
|---|---|
| Component | PhotoPicker |
| Description | Añade/actualiza la foto de una planta (cámara o galería) | 
| Category | input |
| RN Paper / libs | `expo-image-picker` + `Avatar.Image` / `Button` |

### States

| State | Description | Trigger |
|---|---|---|
| empty | Sin foto: botón "+ Añadir foto" + marcador por defecto | planta sin foto |
| has-photo | Muestra la foto + acción "Cambiar" | foto asignada |
| uploading | Subiendo/procesando la imagen | tras elegir imagen |
| permission-denied | Permiso de cámara/galería denegado | usuario deniega |
| error | Fallo al subir/leer la imagen | fallo de I/O |

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| uri | string \| null | no | null | Foto actual (null = sin foto) |
| onPick | (uri) => void | yes | — | Devuelve la imagen elegida |
| onRemove | () => void | no | — | Quita la foto |

### Behaviour (permiso, HU-B2)

- La foto es **opcional al crear** (Q5 producto): nunca bloquea Guardar.
- El **permiso de Android** (cámara/galería) se pide **en contexto**, solo al tocar
  "+ Añadir foto", con explicación previa. Si se deniega → estado `permission-denied`:
  la planta se guarda sin foto y se muestra el marcador; se puede reintentar luego.
- *(Librería/almacenamiento concretos diferidos a Application/NFR Design —
  OQ-refined-2 y OQ-2.)*

### Accessibility

| Requisito | Implementación |
|---|---|
| Rol | button ("Añadir foto"/"Cambiar foto") |
| Label | la imagen lleva `accessibilityLabel` ("Foto de {planta}"); el marcador se anuncia como "sin foto" |
| Lector | anuncia estado subiendo/denegado/error |
| Foco | tras elegir/quitar, foco vuelve al control |

Traza: HU-B2.

---

## ZoneCard (tarjeta de zona)

| Field | Value |
|---|---|
| Component | ZoneCard |
| Description | Tarjeta de zona en la lista de Zonas; abre editar zona | 
| Category | navigation/display |
| RN Paper | `Card` + `TouchableRipple` |

> Distinta de `PlantCard`: **no** muestra humedad/estado, sino métricas de zona.

### Props / Inputs

| Prop | Type | Required | Default | Description |
|---|---|---|---|---|
| name | string | yes | — | Nombre de la zona |
| plantCount | number | yes | — | Nº de plantas en la zona |
| alertCount | number | yes | 0 | Nº de plantas en alerta (BAJA) |
| onPress | () => void | yes | — | Abre editar zona (§7b) |

### States

| State | Description | Trigger |
|---|---|---|
| default | Nombre + "{n} plantas · {m} en alerta" | carga |
| pressed | Ripple | tap |
| loading | Skeleton | fetch |

### Accessibility

| Requisito | Implementación |
|---|---|
| Rol | button/link |
| Label | "Balcón, 6 plantas, 1 en alerta" |
| No solo color | "en alerta" en texto, no solo color |

Traza: HU-D2.

---

## PlantCheckboxList / LanguageSelector (specs breves)

| Componente | RN Paper | Props clave | States | A11y | Traza |
|---|---|---|---|---|---|
| PlantCheckboxList | `Checkbox.Item` (lista) | `plants[]`, `selectedIds[]`, `onToggle(id)` | default / empty ("sin plantas aún") | cada ítem = checkbox etiquetado con el nombre; estado marcado anunciado | HU-D5 |
| LanguageSelector | `SegmentedButtons` | `value: "es"\|"en"`, `onChange` | es / en | rol radio-group; opción seleccionada anunciada; cambia 100% del texto (i18n) | HU-X1 |

---

## EmptyState / ErrorBanner / LoadingSkeleton (estados compartidos)

| Componente | RN Paper | Uso | Traza |
|---|---|---|---|
| EmptyState | `Surface` + ícono + texto + `Button` | listas vacías con CTA de creación | HU-B3, HU-D2, HU-F2 |
| ErrorBanner | `Banner` con acción Reintentar | fallo de polling/Supabase (no bloquea) | user-flow (error de red), HU-E2 |
| LoadingSkeleton | placeholders atenuados | carga de listas/detalle | todas |

### Accessibility (compartida)

- EmptyState: el CTA es un botón etiquetado; el texto guía es legible por lector.
- ErrorBanner: se anuncia como alerta (role status/alert); acción Reintentar accesible.
- Skeleton: marcado como "cargando" para el lector (evitar leer placeholders).

---

## Cobertura de componentes por pantalla

| Pantalla (mockups.md) | Componentes |
|---|---|
| Login / Registro | FormField, Button, LanguageSelector |
| Dashboard | HumidityBadge, PlantCard, AlertItem, EmptyState, ErrorBanner, LoadingSkeleton |
| Plantas lista | PlantCard, EmptyState, ErrorBanner |
| Planta detalle | HumidityBadge, ConfirmDialog, (sensor list), PhotoPicker |
| Crear/editar planta | FormField, ZoneSelector, PhotoPicker, Button |
| Asignar sensor | FormField, Button |
| Zonas lista | ZoneCard, EmptyState, ErrorBanner |
| Crear/editar zona | FormField, PlantCheckboxList, ConfirmDialog |
| Perfil | LanguageSelector, PushToggle, ConfirmDialog |
| Permiso push (§9) | PushPriming (Button x2) |
