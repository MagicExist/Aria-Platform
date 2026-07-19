# Historias de Usuario — Aria Plants

> Etapa 2.4 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Deriva de `requirements.md` (2.3) y `team-practices.md` (2.2); personas en
> `personas.md`. Respuestas de `user-stories-questions.md`: Q1=A (una persona),
> Q2=A (desglose por grupo de requisito), Q3=A (una historia por operación CRUD),
> Q4=B (in-app Must / push Should), Q5 (regla zona-primero), Q6=A (umbral 30%).
>
> **Formato**: "Como [persona], quiero [objetivo], para [beneficio]".
> **Criterios de aceptación** en Given/When/Then (BDD) — guardrail de fase Inception.
> **Prioridad** MoSCoW por historia (informa la frontera del MVP en Delivery
> Planning 2.8; no la fija aquí). **Trazabilidad** RF/NFR y **dependencias** por historia.
> Persona única: **El Cuidador** (ver `personas.md`).

## Convenciones de prioridad (MoSCoW)

- **Must** — sin esto el MVP no cumple su propósito (centro de info de humedad + aviso).
- **Should** — importante pero el walking skeleton no depende de ello; puede caer si aprieta el tiempo.
- **Could** — deseable, se hace solo si sobra margen.
- **Won't (MVP)** — explícitamente fuera de alcance (ver `requirements.md` → Out of Scope).

---

## EPIC A · Cuentas y sesión (RF-A)

### HU-A1 · Registrar cuenta
**Como** El Cuidador, **quiero** crear una cuenta con correo y contraseña, **para** guardar mis plantas y datos de forma privada.
- **Prioridad**: Must · **Traza**: RF-A1 · **Depende de**: — · **Walking skeleton**: sí (habilita login)
- **Criterios (BDD)**:
  - Dado un correo no registrado y una contraseña válida, cuando me registro, entonces se crea la cuenta y quedo autenticado.
  - Dado un correo ya registrado, cuando intento registrarme, entonces veo un mensaje de error y no se crea una cuenta duplicada.
  - Dada una contraseña que no cumple la política por defecto de Supabase Auth (S-5), cuando intento registrarme, entonces veo un error explicando el requisito y no se crea la cuenta.
- **INVEST**: independiente; testable con cuenta nueva; pequeña.

### HU-A2 · Iniciar sesión
**Como** El Cuidador, **quiero** iniciar sesión con mi correo y contraseña, **para** acceder a mis plantas desde mi dispositivo.
- **Prioridad**: Must · **Traza**: RF-A2 · **Depende de**: HU-A1 · **Walking skeleton**: sí
- **Criterios (BDD)**:
  - Dadas credenciales correctas, cuando inicio sesión, entonces obtengo una sesión activa y llego a la pantalla principal.
  - Dadas credenciales incorrectas, cuando intento iniciar sesión, entonces veo un mensaje de error y no se crea sesión.
- **INVEST**: independiente; valor claro (acceso); testable.

### HU-A3 · Cerrar sesión
**Como** El Cuidador, **quiero** cerrar sesión, **para** proteger mi información en un dispositivo compartido.
- **Prioridad**: Must · **Traza**: RF-A3 · **Depende de**: HU-A2 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una sesión activa, cuando cierro sesión, entonces vuelvo a la pantalla de login.
  - Dado que cerré sesión, cuando intento abrir una pantalla protegida, entonces se me redirige a login y no veo datos.
- **INVEST**: pequeña; testable.

### HU-A4 · Aislamiento de mis datos (privacidad por usuario)
**Como** El Cuidador, **quiero** que solo yo pueda ver y modificar mis plantas, zonas, sensores y lecturas, **para** confiar en que mi información es privada.
- **Prioridad**: Must · **Traza**: RF-A4, NFR-Seguridad · **Depende de**: HU-A2 · **Walking skeleton**: sí (se valida en el skeleton)
- **Criterios (BDD)**:
  - Dadas dos cuentas distintas con datos propios, cuando la cuenta B consulta cualquier recurso, entonces nunca aparecen plantas/zonas/sensores/lecturas de la cuenta A.
  - Dado el cliente de la app, cuando se inspecciona su configuración, entonces solo contiene la clave anónima pública de Supabase (nunca la service key).
- **INVEST**: transversal pero testable con dos cuentas; base de RLS.

---

## EPIC D · Zonas de riego (RF-D)

> **Regla zona-primero (Q5)**: debe existir ≥1 zona antes de poder crear plantas;
> cada planta pertenece a exactamente una zona (no hay zona por defecto implícita).

### HU-D1 · Crear zona de riego
**Como** El Cuidador, **quiero** crear una zona de riego, **para** poder organizar mis plantas por grupos y habilitar el registro de plantas.
- **Prioridad**: Must · **Traza**: RF-D1 · **Depende de**: HU-A2 · **Walking skeleton**: sí (prerequisito de crear planta)
- **Criterios (BDD)**:
  - Dado que estoy autenticado, cuando creo una zona con nombre, entonces la zona aparece en mi lista de zonas.
  - Dado que aún no tengo ninguna zona, cuando entro a crear una planta, entonces la app me guía primero a crear una zona (no permite crear planta sin zona).
- **INVEST**: pequeña; independiente; testable.

### HU-D2 · Ver mis zonas
**Como** El Cuidador, **quiero** ver la lista de mis zonas de riego, **para** elegir dónde colocar cada planta y navegar por grupos.
- **Prioridad**: Must · **Traza**: RF-D1 · **Depende de**: HU-D1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dadas una o más zonas creadas, cuando abro la lista de zonas, entonces veo todas mis zonas con su nombre.
  - Dado que selecciono una zona, cuando abro su detalle, entonces veo las plantas asignadas a esa zona.
- **INVEST**: pequeña; testable.

### HU-D3 · Editar una zona
**Como** El Cuidador, **quiero** editar el nombre de una zona, **para** corregir o reorganizar mi estructura.
- **Prioridad**: Should · **Traza**: RF-D1 (gestión) · **Depende de**: HU-D1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una zona existente, cuando cambio su nombre y guardo, entonces el cambio persiste y se refleja en la lista y en las plantas de esa zona.
- **INVEST**: pequeña; testable.

### HU-D4 · Eliminar una zona
**Como** El Cuidador, **quiero** eliminar una zona que ya no uso, **para** mantener limpia mi organización.
- **Prioridad**: Should · **Traza**: RF-D1 (gestión) · **Depende de**: HU-D1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una zona **sin plantas**, cuando la elimino y confirmo, entonces desaparece de la lista de zonas.
  - Dada una zona **con plantas**, cuando intento eliminarla, entonces la app impide el borrado (o exige mover/eliminar sus plantas primero) y me explica por qué. *(El comportamiento exacto de cascada se decide en Application Design — relacionado con OQ-3.)*
- **INVEST**: testable; la regla con plantas queda acotada en diseño.

### HU-D5 · Reasignar una planta a otra zona
**Como** El Cuidador, **quiero** mover una planta de una zona a otra, **para** reorganizar cuando cambio la disposición física.
- **Prioridad**: Should · **Traza**: RF-D2 · **Depende de**: HU-B1, HU-D1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una planta asignada a la zona X, cuando la reasigno a la zona Y, entonces la planta aparece en Y y deja de aparecer en X (sin duplicarse).
  - Dada una planta, cuando consulto su detalle, entonces muestra exactamente una zona.
- **INVEST**: pequeña; testable; refuerza la cardinalidad 1-zona.

---

## EPIC B · Gestión de plantas (RF-B)

### HU-B1 · Crear una planta (dentro de una zona)
**Como** El Cuidador, **quiero** registrar una planta con su nombre y zona, **para** empezar a monitorearla.
- **Prioridad**: Must · **Traza**: RF-B1, RF-D2 · **Depende de**: HU-D1 (regla zona-primero) · **Walking skeleton**: sí
- **Criterios (BDD)**:
  - Dado que tengo al menos una zona, cuando creo una planta con **nombre** (obligatorio) y elijo una **zona**, entonces se crea con **fecha de registro automática** y aparece en el listado y en su zona.
  - Dado que **no** tengo ninguna zona creada, cuando intento crear una planta, entonces no puedo continuar y se me dirige a crear una zona primero (regla zona-primero).
  - Dado el formulario de creación, cuando no indico nombre, entonces no puedo guardar y veo el error del campo obligatorio.
  - Dado el formulario de creación, cuando dejo vacíos especie/tipo (texto libre) y foto, entonces la planta se crea igualmente (campos opcionales).
- **INVEST**: valor claro; testable; pequeña (la foto va en HU-B2).

### HU-B2 · Adjuntar o actualizar la foto de una planta
**Como** El Cuidador, **quiero** poner una foto a mi planta, **para** reconocerla de un vistazo.
- **Prioridad**: Must (feature) — **opcional al crear** (Q5) · **Traza**: RF-B1 (foto) · **Depende de**: HU-B1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una planta sin foto, cuando le añado una foto, entonces la foto se muestra en el detalle (y en el listado si aplica).
  - Dada una planta con foto, cuando la reemplazo, entonces se muestra la nueva foto.
  - Dado que creo una planta sin foto, entonces la planta es válida y se muestra un marcador por defecto en lugar de la foto.
- **INVEST**: independiente de HU-B1; testable. *(Almacenamiento de la foto: OQ-2, a decidir en Application Design.)*

### HU-B3 · Ver el listado de mis plantas
**Como** El Cuidador, **quiero** ver todas mis plantas en una lista, **para** tener el inventario a la mano.
- **Prioridad**: Must · **Traza**: RF-B2 · **Depende de**: HU-B1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dadas una o más plantas, cuando abro el listado, entonces veo cada planta con su nombre (y foto si existe).
  - Dado que no tengo plantas, cuando abro el listado, entonces veo un estado vacío que invita a crear una planta.
- **INVEST**: pequeña; testable.

### HU-B4 · Ver el detalle de una planta
**Como** El Cuidador, **quiero** abrir el detalle de una planta, **para** ver toda su información y sus sensores.
- **Prioridad**: Must · **Traza**: RF-B2, RF-C2 · **Depende de**: HU-B1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una planta, cuando abro su detalle, entonces veo nombre, especie (si existe), foto (si existe), su zona y los sensores asignados con su última lectura de humedad.
- **INVEST**: testable; agrega valor de consulta.

### HU-B5 · Editar una planta
**Como** El Cuidador, **quiero** editar los datos de una planta, **para** corregir información o actualizar su especie/zona.
- **Prioridad**: Should · **Traza**: RF-B3 · **Depende de**: HU-B1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una planta existente, cuando edito sus datos y guardo, entonces los cambios persisten y se reflejan al reabrir el detalle.
  - Dado que borro el nombre al editar, entonces no puedo guardar (nombre sigue obligatorio).
- **INVEST**: pequeña; testable.

### HU-B6 · Eliminar una planta
**Como** El Cuidador, **quiero** eliminar una planta que ya no tengo, **para** que mi inventario refleje la realidad.
- **Prioridad**: Should · **Traza**: RF-B4 · **Depende de**: HU-B1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una planta, cuando la elimino y confirmo, entonces desaparece del listado y de su zona.
  - Dada una planta con sensores asignados, cuando la elimino, entonces sus sensores se tratan según la regla definida en Application Design (cascada vs. desasignar — OQ-3). *(Historia acotada; la regla exacta se fija en diseño.)*
- **INVEST**: testable; dependencia de datos marcada.

---

## EPIC C · Sensores (RF-C)

### HU-C1 · Registrar y asignar un sensor a una planta
**Como** El Cuidador, **quiero** registrar un sensor y asignarlo a una planta, **para** empezar a recibir sus lecturas de humedad.
- **Prioridad**: Must · **Traza**: RF-C1 · **Depende de**: HU-B1 · **Walking skeleton**: sí
- **Criterios (BDD)**:
  - Dada una planta, cuando registro un sensor y lo asigno a ella, entonces el sensor aparece bajo esa planta.
  - Dada una planta con un sensor asignado, cuando asigno un segundo sensor, entonces la planta lista 2+ sensores (una planta admite varios sensores).
- **INVEST**: valor claro (habilita lecturas); testable.

### HU-C2 · Ver los sensores de una planta con su última lectura
**Como** El Cuidador, **quiero** ver los sensores de una planta y su última lectura de humedad, **para** saber de dónde viene el dato.
- **Prioridad**: Must · **Traza**: RF-C2, RF-E2 · **Depende de**: HU-C1 · **Walking skeleton**: sí (parte de "ver humedad")
- **Criterios (BDD)**:
  - Dada una planta con sensores, cuando abro su detalle, entonces cada sensor muestra su última lectura de humedad conocida.
  - Dado un sensor sin lecturas aún, cuando lo veo, entonces se muestra un estado "sin lectura todavía" sin romper la vista.
- **INVEST**: testable; consulta.

> **Nota de alcance (Could / no MVP explícito)**: `requirements.md` (RF-C) solo
> define registrar/asignar y ver sensores. **Editar** o **desasignar/eliminar** un
> sensor no está en RF-C; se marca como **Could** y se decidirá si entra según lo
> que resuelva Application Design sobre el ciclo de vida sensor↔planta (relacionado
> con OQ-3). No se detallan criterios de aceptación hasta esa decisión.

---

## EPIC E · Dashboard / visualización de humedad (RF-E)

### HU-E1 · Ver el dashboard de humedad
**Como** El Cuidador, **quiero** un dashboard que liste mis plantas con su humedad actual, **para** ver el estado general de un vistazo.
- **Prioridad**: Must · **Traza**: RF-E1, RF-E3 · **Depende de**: HU-B1, HU-C1 · **Walking skeleton**: sí
- **Criterios (BDD)**:
  - Dadas plantas con sensores, cuando abro el dashboard, entonces veo mis plantas con su humedad actual dentro de la vista (no en pantalla dedicada).
  - Dado el MVP, cuando miro cualquier vista, entonces solo se muestra la variable **humedad** (no luz ni otras).
- **INVEST**: valor central; testable.

### HU-E2 · Humedad actualizada por polling (~5 min)
**Como** El Cuidador, **quiero** que la humedad mostrada refleje la última lectura sin tener que refrescar manualmente, **para** confiar en que el dato está al día.
- **Prioridad**: Must · **Traza**: RF-E2, NFR-Frescura · **Depende de**: HU-E1 · **Walking skeleton**: sí
- **Criterios (BDD)**:
  - Dado el dashboard abierto, cuando llega una lectura más reciente, entonces la humedad mostrada se actualiza en el siguiente ciclo de polling (≤ ~5 min).
  - Dado que pasa un intervalo de polling sin nueva lectura, entonces se sigue mostrando la última lectura conocida (sin error ni bloqueo de la UI; el MVP no marca "sin dato" por antigüedad).
- **INVEST**: testable con lecturas simuladas (S-2); acota frescura.

---

## EPIC F · Alertas de humedad baja (RF-F)

### HU-F1 · Recibir alerta cuando la humedad baja del umbral
**Como** El Cuidador, **quiero** que se genere una alerta cuando una planta baja del 30% de humedad, **para** regar antes de que se dañe.
- **Prioridad**: Must · **Traza**: RF-F1 (umbral 30% fijo, Q6=A) · **Depende de**: HU-C1, HU-E2 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una lectura de humedad **< 30%**, cuando el sistema la procesa, entonces se genera una alerta para esa planta/sensor.
  - Dada una lectura de humedad **≥ 30%**, cuando el sistema la procesa, entonces **no** se genera alerta.
  - Dado que una planta ya tiene una alerta activa por humedad baja, cuando llegan nuevas lecturas aún por debajo del umbral, entonces no se generan alertas duplicadas repetidas (una alerta activa por condición).
- **INVEST**: testable con lecturas simuladas; regla de negocio clara.

### HU-F2 · Ver mis alertas recientes en la app
**Como** El Cuidador, **quiero** una lista de alertas recientes dentro de la app, **para** revisar qué plantas necesitan (o necesitaron) atención.
- **Prioridad**: Must (Q4=B: in-app es Must) · **Traza**: RF-F2 (in-app) · **Depende de**: HU-F1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una alerta generada, cuando abro "alertas recientes", entonces la alerta aparece con la planta y el momento.
  - Dado que no hay alertas, cuando abro la lista, entonces veo un estado vacío ("sin alertas recientes").
- **INVEST**: testable; valor de consulta.

### HU-F3 · Recibir una notificación push de la alerta
**Como** El Cuidador, **quiero** recibir una notificación push cuando una planta cruza el umbral, **para** enterarme aunque no tenga la app abierta.
- **Prioridad**: Should (Q4=B: push es Should) · **Traza**: RF-F2 (push), S-3 · **Depende de**: HU-F1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dado que concedí permiso de notificaciones, cuando una planta cruza a humedad < 30%, entonces recibo una notificación push aunque la app esté cerrada.
  - Dado que **no** concedí permiso de notificaciones, cuando ocurre una alerta, entonces igualmente aparece en "alertas recientes" in-app (la falta de push no rompe el flujo).
- **INVEST**: independiente de in-app (degradación limpia); testable. *(Mecanismo de push y permisos Android: OQ-4, S-3 — detalle en Application/NFR Design.)*

### HU-F4 · La alerta se resuelve sola al recuperar humedad
**Como** El Cuidador, **quiero** que la alerta se marque como resuelta cuando la humedad vuelve a rango normal, **para** no tener que descartarla manualmente.
- **Prioridad**: Must · **Traza**: RF-F3 · **Depende de**: HU-F1 · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dada una alerta activa por humedad baja, cuando llega una lectura **≥ 30%**, entonces la alerta pasa a estado **resuelto** sin intervención del usuario.
  - Dado el MVP, cuando existe una alerta, entonces no hay descarte manual ni recordatorios repetidos.
- **INVEST**: testable con secuencia de lecturas; cierra el ciclo de la alerta.

---

## Historias transversales (experiencia / NFR con cara de usuario)

### HU-X1 · Cambiar el idioma de la app (ES/EN)
**Como** El Cuidador, **quiero** alternar el idioma entre español e inglés, **para** usar la app en mi idioma preferido.
- **Prioridad**: Must · **Traza**: NFR-Usabilidad/UI (i18n) · **Depende de**: — · **Walking skeleton**: no
- **Criterios (BDD)**:
  - Dado el selector de idioma, cuando cambio de ES a EN, entonces el 100% de los textos visibles cambia de idioma.
  - Dada una revisión de lint/código, cuando se buscan textos de UI, entonces no hay strings hardcodeados fuera de i18n.
  - Dado un primer arranque sin idioma guardado, cuando la app inicia, entonces se muestra en **español** (idioma de trabajo por defecto).
- **INVEST**: testable; convención de proyecto.

> **Nota de diseño (dark-first, no es historia de acción)**: el **tema oscuro es el
> principal** (dark-first) en el MVP; el tema claro es post-MVP, por lo que no hay
> historia de "cambiar tema" en este alcance. Se verifica como criterio de arranque:
> - Dado un primer arranque, cuando la app carga, entonces el tema activo es **oscuro**.

---

## Matriz de trazabilidad RF/NFR → Historias

| Requisito | Historia(s) |
|-----------|-------------|
| RF-A1 Registro | HU-A1 |
| RF-A2 Login | HU-A2 |
| RF-A3 Logout | HU-A3 |
| RF-A4 Aislamiento (RLS) | HU-A4 |
| RF-B1 Crear planta (+foto opcional) | HU-B1, HU-B2 |
| RF-B2 Ver listado/detalle | HU-B3, HU-B4 |
| RF-B3 Editar planta | HU-B5 |
| RF-B4 Eliminar planta | HU-B6 |
| RF-C1 Registrar/asignar sensor | HU-C1 |
| RF-C2 Ver sensores + última lectura | HU-C2, HU-B4 (detalle muestra sensores) |
| RF-D1 Crear/ver/gestionar zonas | HU-D1, HU-D2, HU-D3, HU-D4 |
| RF-D2 Asignar/reasignar planta a zona | HU-B1 (asignación), HU-D5 (reasignación) |
| RF-E1 Dashboard humedad | HU-E1 |
| RF-E2 Polling ~5 min | HU-E2, HU-C2 (última lectura por sensor) |
| RF-E3 Solo humedad | HU-E1 (criterio) |
| RF-F1 Umbral 30% | HU-F1 |
| RF-F2 Entrega alerta (in-app + push) | HU-F2 (in-app), HU-F3 (push) |
| RF-F3 Auto-resolución | HU-F4 |
| NFR-Plataforma (Android/APK) | transversal a todas (contexto de ejecución) |
| NFR-Seguridad (anon key/RLS) | HU-A4 |
| NFR-Frescura | HU-E2 |
| NFR-Usabilidad/UI (i18n, dark) | HU-X1 (+ nota dark-first) |
| NFR-Calidad (tests/CI/lint) | transversal (aplica a toda historia; se verifica en Build & Test / CI) |

**Cobertura**: todos los RF-A…RF-F y los NFR con superficie de usuario tienen ≥1
historia. Los NFR de plataforma y calidad son transversales (no se modelan como
historia individual; se aplican como criterios de "hecho" en construcción).

## Mapeo al Walking Skeleton (input para Delivery Planning 2.8)

El walking skeleton de `team-practices.md` es **login → registrar planta → asignar
sensor → ver humedad**. Con la **regla zona-primero (Q5)**, la rebanada de extremo
a extremo requiere crear una zona antes de la planta. Historias del skeleton:

1. HU-A1 Registrar cuenta → 2. HU-A2 Iniciar sesión → 3. **HU-D1 Crear zona**
(prerequisito nuevo) → 4. HU-B1 Crear planta → 5. HU-C1 Asignar sensor →
6. HU-E1 + HU-E2 Ver humedad (dashboard + polling). HU-A4 (aislamiento) se valida
dentro del skeleton como punto de integración crítico.

> **Señal para Delivery Planning**: el skeleton descrito en las prácticas no
> mencionaba "crear zona"; Q5 lo convierte en paso obligatorio previo a crear
> planta. Debe incluirse en el Bolt 1.

## Resumen MoSCoW

| Prioridad | Historias | Total |
|-----------|-----------|-------|
| **Must** | HU-A1, HU-A2, HU-A3, HU-A4, HU-D1, HU-D2, HU-B1, HU-B2, HU-B3, HU-B4, HU-C1, HU-C2, HU-E1, HU-E2, HU-F1, HU-F2, HU-F4, HU-X1 | 18 |
| **Should** | HU-D3, HU-D4, HU-D5, HU-B5, HU-B6, HU-F3 | 6 |
| **Could** | Editar/desasignar sensor (sin HU formal; ver nota EPIC C) | 0 formales |
| **Won't (MVP)** | Salud derivada, variable luz, multiusuario/compartir, umbrales personalizados, web/iOS, tiendas, notas libres, "sin dato" por antigüedad, descarte manual de alertas, histórico/tendencias | — |

**Total historias formales**: 24 (18 Must + 6 Should). La frontera formal del MVP
la decide Delivery Planning (2.8); estas prioridades la informan.
