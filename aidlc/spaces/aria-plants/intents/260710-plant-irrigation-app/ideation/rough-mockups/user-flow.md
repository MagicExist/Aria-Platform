# User Flow — Aria Plants (Android MVP)

> Etapa 1.6 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Flujos de usuario derivados del `scope-document.md`, el `intent-backlog.md` y
> el `intent-statement.md`. Complementa `wireframes.md`.

## Flujo principal (happy path) — Walking Skeleton

Es la primera rebanada de extremo a extremo del `scope-document.md`:

```
[Abrir app]
    |
    v
[No autenticado?] --si--> [Login] --(crear cuenta)--> [Registro]
    | no                     |                            |
    |                        +-------------+--------------+
    v                                      v
[Dashboard] <-------------------- [Sesion iniciada]
    |
    | (primer uso: sin plantas -> estado vacio con guia)
    v
[Plantas] -> [ + Nueva planta ]
    |
    v
[Crear planta: nombre + zona + asignar sensor de humedad]
    |
    |  (cuenta nueva sin zonas: el selector de Zona ofrece
    |   "crear zona" inline -> se crea la primera zona aqui)
    v
[Guardar] --> [Detalle de planta: humedad actual]
    |
    v
[Dashboard muestra la planta y su humedad]
```

<!-- Text fallback: al abrir, si no hay sesion va a Login (o Registro). Tras
autenticarse llega al Dashboard. En primer uso crea una planta (nombre, zona,
sensor de humedad); si aun no existe ninguna zona, el selector de zona permite
crear la primera zona inline. Guarda, ve el detalle con la humedad, y el
Dashboard refleja la planta. -->

Nota de bootstrap: el walking skeleton requiere al menos 1 zona. Se resuelve
permitiendo crear la zona inline desde el formulario de planta; alternativamente
el usuario puede ir primero a Zonas -> + Nueva zona. Ambas rutas quedan cubiertas.

## Flujo de alertas (humedad fuera de rango)

```
[Backend/Supabase actualiza lecturas de humedad]
    |
    v
[App consulta por polling]
    |
    v
[Humedad < umbral fijo (p.ej. 30%)?]
    | si
    +--> [Marca planta como "BAJA"]
    +--> [Muestra alerta en seccion "Alertas recientes" del Dashboard]
    +--> [Envia notificacion push al telefono]
```

<!-- Text fallback: la app consulta por polling las lecturas en Supabase; si la
humedad de una planta cae bajo el umbral fijo, marca la planta como BAJA, la
muestra en las alertas recientes del Dashboard y envia una notificacion push. -->

## Flujos secundarios

- **Gestionar plantas:** Plantas -> tocar planta -> Detalle -> Editar / Eliminar
  (con confirmacion) / Asignar o quitar sensor.
- **Gestionar zonas:** Zonas -> + Nueva zona (o tocar una zona -> Editar zona) ->
  nombrar y marcar las plantas que pertenecen a la zona -> Guardar (o Eliminar
  zona con confirmacion). Una planta pertenece a una zona (MVP: al menos 1 zona).
- **Ajustes:** Perfil -> cambiar idioma (ES/EN), tema (oscuro, fijo en el MVP),
  toggle de notificaciones push, umbral de humedad global (fijo) -> Cerrar sesion.

## Navegación global

Tabs inferiores persistentes en las pantallas principales:

```
[ Inicio (Dashboard) ] [ Plantas ] [ Zonas ] [ Perfil ]
```

Auth (Login/Registro) y los formularios de crear/editar se presentan fuera de
los tabs (pila modal / pantalla completa con boton atras).

## Estados y recuperación

- **Sin sesion** -> Login. Sesion expirada -> volver a Login conservando destino.
- **Sin datos** (primer uso) -> estados vacios con llamada a la accion (crear
  primera planta / primera zona).
- **Sin lectura de sensor** -> mostrar humedad "sin dato" en vez de un valor falso.
- **Error de red al consultar Supabase** -> banner de error + opcion reintentar;
  no se bloquea la navegacion.

## Fuera del MVP (no aparecen en estos flujos)

Indicador de "salud", variable de luz, histórico/tendencias, multiusuario,
umbrales personalizados por planta, recuperación de contraseña y tema claro
(ver `scope-document.md`). Estos dos últimos son diferidos de bajo esfuerzo que
podrían entrar rápido post-MVP.
