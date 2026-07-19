# Requirements Analysis — Aria Plants (App Android de Monitoreo de Riego)

> Etapa 2.3 · Fase Inception · Alcance: **MVP** (profundidad **Estándar**) · Fecha: 2026-07-11
> Deriva de `intent-statement.md` (intent-capture), `scope-document.md`
> (scope-definition) y `team-practices.md` (practices-discovery). Cada requisito
> tiene un criterio de aceptación verificable (pass/fail).
> Idioma de trabajo: español (la UI final pasa por i18n ES/EN).

## Análisis de Intent (Objetivos)

De acuerdo con `intent-statement.md`, Aria Plants es un **centro de información**
para las plantas de un cuidador doméstico: reúne en un solo lugar las lecturas de
sensores por planta, permite organizarlas por zonas de riego y avisa cuando una
variable sale de rango, con la meta de optimizar el riego y reducir pérdidas. Es
además un proyecto de aprendizaje: construir una app **Android** (React
Native/Expo) que consume datos de un backend de sensores es un objetivo en sí.

`scope-document.md` acota el MVP a **una sola variable (humedad)**, umbrales
**fijos**, una zona de riego inicial (con capacidad de crecer) y distribución como
**APK Android**. Este documento traduce ese alcance en requisitos verificables e
incorpora las decisiones tomadas en las preguntas de clarificación
(`requirements-analysis-questions.md`).

## Modelo de Dominio (cardinalidades)

Resultado de **P2 = B**:

```
[Usuario] 1 ---- * [Zona de riego]
[Zona de riego] 1 ---- * [Planta]        (una planta pertenece a UNA zona)
[Planta] 1 ---- * [Sensor]               (una planta puede tener VARIOS sensores)
[Sensor] 1 ---- * [Lectura de humedad]   (solo-lectura para la app en el MVP)
```
<!-- Text fallback: un usuario tiene varias zonas; una zona agrupa varias plantas; cada planta pertenece a una sola zona y puede tener varios sensores; cada sensor produce muchas lecturas de humedad que la app solo lee. -->

## Requisitos Funcionales

### RF-A · Cuentas y sesión (Supabase Auth)
- **RF-A1** — Registro de cuenta con **correo + contraseña** (P5=A; sin OAuth ni
  verificación obligatoria en el MVP).
  *Aceptación:* dado un correo no registrado y una contraseña válida, al
  registrarse se crea la cuenta y el usuario queda autenticado.
- **RF-A2** — Inicio de sesión con correo + contraseña.
  *Aceptación:* credenciales correctas → sesión activa; incorrectas → mensaje de
  error y sin sesión.
- **RF-A3** — Cierre de sesión.
  *Aceptación:* tras cerrar sesión, las pantallas protegidas dejan de ser
  accesibles y se vuelve a la pantalla de login.
- **RF-A4** — Aislamiento de datos por usuario (RLS activo).
  *Aceptación:* un usuario nunca ve plantas/zonas/sensores/lecturas de otro
  usuario (verificable con dos cuentas distintas).

### RF-B · Gestión de plantas (CRUD)
- **RF-B1** — Crear planta con campos (P1=A,B,C,E): **nombre (obligatorio)**,
  **especie/tipo (texto libre, opcional)**, **foto (opcional)**, **fecha de
  registro (automática)**. *No* se incluye notas/descripción libre.
  *Aceptación:* sin nombre no se puede guardar; con nombre se crea y aparece en el
  listado con su fecha de registro automática.
- **RF-B2** — Ver el listado de plantas del usuario y el detalle de cada planta.
  *Aceptación:* el detalle muestra nombre, especie, foto (si existe), zona y
  sensores asignados.
- **RF-B3** — Editar los datos de una planta.
  *Aceptación:* los cambios persisten y se reflejan al reabrir el detalle.
- **RF-B4** — Eliminar una planta.
  *Aceptación:* tras confirmar, la planta desaparece del listado; se define en
  Application Design el efecto sobre sus sensores asignados (ver Preguntas
  Abiertas).

### RF-C · Sensores
- **RF-C1** — Registrar un sensor y **asignarlo a una planta**; una planta admite
  **varios** sensores (P2=B).
  *Aceptación:* un sensor asignado aparece bajo su planta; una planta puede listar
  2+ sensores.
- **RF-C2** — Ver los sensores de una planta.
  *Aceptación:* el detalle de la planta lista sus sensores y la última lectura de
  humedad de cada uno.

### RF-D · Zonas de riego
- **RF-D1** — Crear zonas de riego (mínimo 1 en el MVP).
  *Aceptación:* se puede crear al menos una zona y aparece en la lista de zonas.
- **RF-D2** — Asignar cada planta a **una** zona (P2=B: una planta pertenece a una
  sola zona).
  *Aceptación:* una planta muestra exactamente una zona; reasignar la mueve de
  zona sin duplicarla.

### RF-E · Visualización / Dashboard (humedad)
- **RF-E1** — Dashboard general que muestra plantas/zonas con su lectura de
  humedad **dentro** de la vista (no en pantalla dedicada), según
  `scope-document.md`.
  *Aceptación:* el dashboard lista las plantas del usuario con su humedad actual.
- **RF-E2** — "Humedad actual" = **última lectura registrada**, actualizada por
  **polling cada ~5 min** (P4=A). El MVP *no* marca "sin dato" por antigüedad.
  *Aceptación:* al llegar una lectura más reciente, la humedad mostrada se
  actualiza en el siguiente ciclo de polling (≤ ~5 min).
- **RF-E3** — Solo se soporta la variable **HUMEDAD** en el MVP.
  *Aceptación:* no se muestran otras variables (luz u otras) en ninguna vista.

### RF-F · Alertas de humedad baja
- **RF-F1** — Umbral **solo-mínimo, fijo** (P3=A): se dispara alerta cuando la
  humedad **baja** del mínimo fijo. **Valor por defecto del MVP: 30%**, como
  constante de configuración de la app (no editable por el usuario; ver S-4). El
  valor proviene del ejemplo usado en `intent-statement.md` y en la opción P3, y
  puede ajustarse en el gate de aprobación.
  *Aceptación:* con humedad < 30% se genera alerta; con humedad ≥ 30% no se genera.
- **RF-F2** — Entrega de la alerta por **notificación push** al cruzar el umbral,
  y visible en la app en una lista de **"alertas recientes"** (P6=A;
  consistente con `scope-document.md` in-app + push).
  *Aceptación:* al cruzar el umbral llega una push y la alerta aparece en "alertas
  recientes".
- **RF-F3** — La alerta **se resuelve sola** cuando la humedad vuelve a rango
  normal (P6=A; sin descarte manual ni recordatorios repetidos en el MVP).
  *Aceptación:* al recuperarse la humedad, la alerta pasa a estado resuelto sin
  intervención del usuario.

## Requisitos No Funcionales (NFR)

- **NFR-Plataforma** — App **solo Android** (React Native/Expo + TypeScript),
  distribuida como **APK** (EAS o build local); sin web ni iOS
  (`scope-document.md`, `team-practices.md` → Deployment).
  *Aceptación:* existe un APK instalable que arranca en un dispositivo Android.
- **NFR-Seguridad** — Solo la **clave anónima pública** de Supabase vive en el
  cliente; RLS activo por usuario; nunca la service key en la app.
  *Aceptación:* revisión de código/config confirma que no hay service key en el
  cliente y que RLS bloquea el acceso cruzado.
- **NFR-Frescura** — La humedad mostrada refleja la última lectura con un desfase
  máximo del intervalo de polling (~5 min) (P4=A).
  *Aceptación:* transcurrido el intervalo de polling sin nueva lectura, el valor
  mostrado sigue siendo la última lectura conocida (sin error ni bloqueo de la UI).
- **NFR-Usabilidad/UI** — Tema **oscuro como principal** (dark-first) y todo el
  texto por **i18n (ES/EN)** con selector de idioma; sin strings hardcodeados
  (convención de proyecto).
  *Aceptación:* la app arranca en tema oscuro por defecto; el selector de idioma
  alterna el 100% de los textos visibles entre ES/EN; una revisión de lint/código
  no encuentra strings de UI fuera de i18n.
- **NFR-Calidad** — Pruebas **junto al código**, piso **~80% de cobertura de
  líneas**, **CI en verde** antes de integrar a `main`; **ESLint + Prettier + TS
  strict** (de `team-practices.md`).
  *Aceptación:* la CI corre lint + tests y bloquea el merge si fallan o si la
  cobertura cae del piso.

## Restricciones (Constraints)

Heredadas de `constraint-register.md` vía `scope-document.md`:
- **C-1** Plataforma única Android (TC-1); stack Expo/RN + TS (TC-2).
- **C-2** Este repositorio contiene **únicamente la app**; el backend de sensores
  se construye por separado (TC-4). La **ingesta** de lecturas hacia Supabase está
  fuera de este repo; la app **solo lee**.
- **C-3** Lecturas por **polling** (TC-5); no hay realtime/streaming en el MVP.
- **C-4** **Contrato de datos de sensores por definir** (TC-6): hasta que exista,
  la app asume un contrato/simulación aislado tras un adaptador.
- **C-5** Autenticación mediante **Supabase Auth** (TC-7).
- **C-6** Sin entornos de servidor staging/producción; backend = Supabase
  gestionado (`team-practices.md` → Deployment).

## Supuestos (Assumptions)

- **S-1** Un único usuario real al inicio (el propio autor); el diseño permite
  crecer a más plantas/zonas/usuarios sin ser requisito del MVP
  (`intent-statement.md`).
- **S-2** Mientras no exista el contrato de sensores (C-4), el origen de lecturas
  se **simula/aísla** tras una capa/adaptador para no bloquear el desarrollo de la
  app.
- **S-3** La entrega push usa el mecanismo estándar del ecosistema Expo; el
  detalle técnico se decide en Application Design.
- **S-4** El umbral mínimo fijo de humedad es un parámetro de configuración de la
  app con **valor por defecto 30%** (no editable por el usuario en el MVP:
  umbrales fijos; ver RF-F1).
- **S-5** La política de contraseñas usa los **valores por defecto de Supabase
  Auth**; el MVP no define una política propia de complejidad/longitud.

## Fuera de Alcance (Out of Scope)

De `scope-document.md` (MoSCoW → Won't) y `intent-statement.md`:
- Indicador derivado de **"salud"** de la planta.
- Variable de **luz** y cualquier variable distinta de humedad.
- **Multiusuario / compartir** plantas o zonas.
- **Umbrales personalizados** por planta/tipo (MVP usa umbrales fijos).
- **Web e iOS**; **publicación en tiendas**.
- **Notas/descripción libre** de la planta (P1 no seleccionó la opción D).
- Marcado de **"sin dato"** por antigüedad de lectura (P4 no seleccionó B).
- Descarte manual de alertas y recordatorios repetidos (P6 no seleccionó B/C).
- **Histórico / tendencias** — "Could" no priorizado; solo si sobra tiempo.

## Preguntas Abiertas (para etapas posteriores)

- **OQ-1** ¿La app incluye una capa de **simulación/ingreso** de lecturas mientras
  no exista el contrato de sensores? Diferido a **Application Design** (ya
  señalado como open question en `scope-document.md`).
- **OQ-2** **Almacenamiento de la foto** de planta (¿Supabase Storage vs. URL?) —
  a decidir en Application Design (por RF-B1).
- **OQ-3** Efecto de eliminar una planta sobre sus **sensores/lecturas**
  asociados (cascade vs. desasignar) — a decidir en Application Design (por RF-B4).
- **OQ-4** Mecanismo concreto de **push** y permisos de notificación en Android —
  detalle técnico para Application Design / NFR Design (por RF-F2).
