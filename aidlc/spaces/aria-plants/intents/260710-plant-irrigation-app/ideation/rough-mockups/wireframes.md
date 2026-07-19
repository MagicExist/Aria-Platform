# Wireframes (baja fidelidad) — Aria Plants (Android)

> Etapa 1.6 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Wireframes conceptuales de baja fidelidad. Derivan del `intent-statement.md`,
> el `scope-document.md` y el `intent-backlog.md`. Estilo: dark-first,
> minimalista, marca "Aria Plants". Solo Android (una columna, vertical).
> Idioma mostrado: español (la app soporta ES/EN con selector).
> Variable del MVP: humedad. Sin pantalla dedicada de alertas (van en Dashboard).

## Convenciones

- Marcos representan una pantalla de teléfono (vertical, una columna).
- `[ ... ]` = botón / control tocable. `( )`/`(o)` = radio. `[x]` = check.
- `____` = campo de texto. Barra inferior = tabs de navegación.
- Diagramas en ASCII básico; ver nota de accesibilidad bajo cada pantalla.

## 1. Autenticación — Login

```
+------------------------------+
|                              |
|          Aria Plants         |
|        (logo / hoja)         |
|                              |
|  Correo                      |
|  __________________________  |
|                              |
|  Contrasena                  |
|  __________________________  |
|                              |
|  [      Iniciar sesion     ] |
|                              |
|  Crear cuenta                |
|                              |
|  Idioma: [ES] [EN]           |
+------------------------------+
```
<!-- Text fallback: pantalla de login con logo Aria Plants, campos correo y
contrasena, boton Iniciar sesion, enlace a crear cuenta, y selector de idioma
ES/EN. (Recuperar contrasena queda fuera del MVP.) -->
Accesibilidad: h1 "Aria Plants"; landmark main con el formulario; foco inicial
en el campo Correo; contraste alto sobre fondo oscuro.

## 2. Autenticación — Registro

```
+------------------------------+
|  <  Crear cuenta             |
|                              |
|  Nombre                      |
|  __________________________  |
|  Correo                      |
|  __________________________  |
|  Contrasena                  |
|  __________________________  |
|  Repetir contrasena          |
|  __________________________  |
|                              |
|  [      Crear cuenta       ] |
|                              |
|  Ya tengo cuenta -> Entrar   |
+------------------------------+
```
<!-- Text fallback: formulario de registro con nombre, correo, contrasena y su
confirmacion, boton Crear cuenta y enlace para volver a login. -->
Accesibilidad: h1 "Crear cuenta"; boton atras (<) en header; foco inicial en
Nombre; validacion inline por campo.

## 3. Dashboard (tab: Inicio)

```
+------------------------------+
|  Aria Plants          (o)perf|
|                              |
|  Resumen                     |
|  +------------+ +----------+ |
|  | Plantas  6 | | Zonas  1 | |
|  +------------+ +----------+ |
|                              |
|  Alertas recientes           |
|  +--------------------------+|
|  | ! Menta: humedad baja    ||
|  | ! Ficus: humedad baja    ||
|  +--------------------------+|
|                              |
|  Plantas (humedad)           |
|  +--------------------------+|
|  | Albahaca      62%   OK   ||
|  | Menta         18%   BAJA ||
|  | Ficus         21%   BAJA ||
|  +--------------------------+|
|                              |
+------------------------------+
| Inicio | Plantas | Zonas |Perf|
+------------------------------+
```
<!-- Text fallback: dashboard con tarjetas de conteo (plantas, zonas), lista de
alertas recientes de humedad, y lista de plantas con su porcentaje de humedad y
estado (OK/BAJA). Barra inferior con tabs Inicio, Plantas, Zonas, Perfil. -->
Accesibilidad: h1 marca + h2 por seccion (Resumen, Alertas recientes, Plantas);
landmarks main (contenido) y nav (tabs inferiores); alertas con icono + texto
(no solo color); foco inicial en el primer elemento de contenido.

## 4. Plantas — Lista (tab: Plantas)

```
+------------------------------+
|  Plantas              [ + ]  |
|                              |
|  +--------------------------+|
|  | Albahaca                 ||
|  | Zona: Balcon  62%  OK    ||
|  +--------------------------+|
|  | Menta                    ||
|  | Zona: Balcon  18%  BAJA  ||
|  +--------------------------+|
|  | Ficus                    ||
|  | Zona: Balcon  21%  BAJA  ||
|  +--------------------------+|
|                              |
+------------------------------+
| Inicio | Plantas | Zonas |Perf|
+------------------------------+
```
<!-- Text fallback: lista de plantas con boton + para agregar y tarjetas por
planta mostrando zona, humedad y estado. Tabs inferiores. (Buscador diferido:
innecesario a la escala del MVP.) -->
Accesibilidad: h1 "Plantas"; boton + etiquetado "Agregar planta"; cada tarjeta
es un enlace al detalle; foco inicial en la primera planta o en el boton +.

## 5. Planta — Detalle

```
+------------------------------+
|  <  Menta            [Editar]|
|                              |
|  Humedad actual              |
|      +------------------+    |
|      |       18%        |    |
|      |   Estado: BAJA   |    |
|      +------------------+    |
|                              |
|  Zona de riego: Balcon       |
|                              |
|  Sensores                    |
|  +--------------------------+|
|  | Sensor HUM-01  (humedad) ||
|  +--------------------------+|
|  [ + Asignar sensor ]        |
|                              |
|  [   Eliminar planta   ]     |
+------------------------------+
```
<!-- Text fallback: detalle de una planta con lectura de humedad actual y estado,
zona asignada, lista de sensores asignados, boton para asignar sensor y boton
para eliminar la planta. Header con atras y Editar. -->
Accesibilidad: h1 con nombre de planta; h2 Humedad/Sensores; valor de humedad
con estado textual (no solo color); confirmacion antes de eliminar.

## 6. Crear / Editar planta (+ asignar sensor)

```
+------------------------------+
|  <  Nueva planta             |
|                              |
|  Nombre                      |
|  __________________________  |
|                              |
|  Zona de riego               |
|  [ Selecciona / crear  v ]   |
|                              |
|  Sensor (humedad)            |
|  [ Selecciona / crear  v ]   |
|                              |
|  Umbral de humedad (fijo)    |
|  Min 30%   (umbral del MVP)  |
|                              |
|  [        Guardar          ] |
+------------------------------+
```
<!-- Text fallback: formulario para crear o editar planta con nombre, selector de
zona (con opcion de crear una zona nueva inline), selector/creacion de sensor de
humedad, umbral fijo informativo, y boton Guardar. El "crear zona" inline resuelve
el bootstrap de la primera zona para una cuenta sin datos. -->
Accesibilidad: h1 "Nueva planta"/"Editar planta"; cada control con label
asociado; selects operables por teclado/lector; foco inicial en Nombre.

## 7. Zonas de riego (tab: Zonas)

```
+------------------------------+
|  Zonas de riego       [ + ]  |
|                              |
|  +--------------------------+|
|  | Balcon                   ||
|  | 6 plantas   1 en alerta  ||
|  +--------------------------+|
|                              |
|  (crea una zona para         |
|   agrupar tus plantas)       |
|                              |
+------------------------------+
| Inicio | Plantas | Zonas |Perf|
+------------------------------+
```
<!-- Text fallback: lista de zonas de riego con boton + para crear, tarjeta por
zona mostrando numero de plantas y cuantas en alerta. Estado vacio con guia.
Tabs inferiores. -->
Accesibilidad: h1 "Zonas de riego"; estado vacio con texto guia; cada tarjeta
abre "Editar zona"; foco inicial en primera zona o en el boton +.

## 7b. Crear / editar zona

```
+------------------------------+
|  <  Nueva zona               |
|                              |
|  Nombre de la zona           |
|  __________________________  |
|                              |
|  Plantas en esta zona        |
|  +--------------------------+|
|  | [x] Albahaca             ||
|  | [x] Menta                ||
|  | [ ] Ficus                ||
|  +--------------------------+|
|                              |
|  [        Guardar          ] |
|  [    Eliminar zona    ]     |
+------------------------------+
```
<!-- Text fallback: formulario para crear o editar una zona de riego con nombre y
una lista de plantas con checkboxes para asignarlas a la zona. Botones Guardar y
Eliminar zona (Eliminar solo al editar). Da paridad de gestion con Plantas y
soporta crear la primera zona. -->
Accesibilidad: h1 "Nueva zona"/"Editar zona"; campo Nombre con label y foco
inicial; lista de plantas como checkboxes etiquetados; confirmacion antes de
eliminar.

## 8. Perfil / Ajustes (tab: Perfil)

```
+------------------------------+
|  Perfil                      |
|                              |
|  Practicante Okuo            |
|  correo@ejemplo.com          |
|                              |
|  Ajustes                     |
|  Idioma            [ES | EN] |
|  Tema: Oscuro (claro: pronto)|
|  Notificaciones push  [x]    |
|  Umbral humedad: 30% (fijo)  |
|                              |
|  [     Cerrar sesion     ]   |
+------------------------------+
| Inicio | Plantas | Zonas |Perf|
+------------------------------+
```
<!-- Text fallback: perfil con datos de la cuenta y ajustes: idioma ES/EN
(selector activo), tema oscuro (unico en el MVP; el claro llega despues), toggle
de notificaciones push, y umbral de humedad global mostrado como valor fijo
informativo (30%). Boton cerrar sesion. Tabs inferiores. -->
Accesibilidad: h1 "Perfil"; h2 "Ajustes"; controles con label; idioma y toggle
push son controles activos; el tema es oscuro fijo en el MVP (claro post-MVP) y
el umbral es informativo (umbrales fijos en el MVP).

## Nota de estados (aplican a todas las listas/lecturas)

Cada pantalla con datos contempla: estado cargando (skeleton), estado vacio
(guia para crear el primer item), estado de error (reintentar) y estado sin
lectura reciente (humedad "sin dato"). Se detallan en refined-mockups (Inception).
