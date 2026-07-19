# Scope Definition — Preguntas de Clarificación

> Etapa 1.4 · Fase Ideación · Alcance: MVP (profundidad Estándar)
> Objetivo: fijar el límite dentro/fuera del MVP y priorizar el backlog.
> Contexto ya decidido: app **solo Android** (Expo/RN+TS), backend **Supabase**,
> lecturas por **polling**, indicador de "salud" **fuera** del MVP.
> Responde escribiendo la(s) letra(s) en `[Answer]:`.

---

## P1. De estas capacidades, ¿cuáles son MUST-HAVE para la primera versión usable del MVP? (elige todas las que apliquen)

A. Login / registro / autenticación (cuenta de usuario con Supabase)
B. Registrar y gestionar plantas (CRUD de plantas)
C. Registrar sensores y asignarlos a una planta
D. Agrupar plantas en zonas de riego
E. Ver las variables (humedad, luz) de cada planta
F. Dashboard/vista general de todas las plantas/zonas
G. Alertas cuando una variable sale de rango
X. Other (especificar)

[Answer]: A, B, C, D, F, G — Must-have: Login/auth, CRUD plantas, Sensores→planta, Zonas de riego, Dashboard general, Alertas. RESUELTO (F1): ver las variables NO es pantalla aparte, se muestran DENTRO del Dashboard general / vista de planta. CAMBIO DE ALCANCE: para el MVP **solo la variable de HUMEDAD** — la LUZ queda FUERA del MVP.

---

## P2. Lo que NO marcaste como must-have arriba, ¿es "Should" (deseable en MVP si da tiempo) o "Won't" (fuera del MVP)?

> Respóndelo en texto si quieres, o elige la postura general:

A. Todo lo no-must es "Should" (intentar incluirlo si el tiempo alcanza)
B. Todo lo no-must es "Won't" (estrictamente fuera del MVP)
C. Mixto — lo detallo en Other
X. Other (especificar)

[Answer]: C — Mixto (derivado de las respuestas). Won't (fuera): indicador de "salud", variable de LUZ, multiusuario/compartir, umbrales personalizados, web/iOS. Could (no priorizado, sin comprometer): histórico/tendencias.

---

## P3. ¿Cómo se entregan las alertas en el MVP?

A. Solo dentro de la app (banner/lista de alertas cuando la abres)
B. Notificaciones push al teléfono (aunque la app esté cerrada)
C. Ambas
D. No definido — recomiéndame lo más simple para el MVP
X. Other (especificar)

[Answer]: C — Ambas (alertas en-app + notificaciones push).

---

## P4. Como los sensores/su contrato aún no existen, ¿debe el MVP permitir ingresar/simular lecturas manualmente para que la app sea usable ya?

A. Sí — permitir ingreso manual o datos simulados (seed) para probar todo el flujo
B. No — la app solo lee de Supabase; poblaré Supabase por fuera con datos de prueba
C. No me importa cómo, mientras pueda ver datos en pantalla durante el desarrollo
X. Other (especificar)

[Answer]: A (con matiz) — El usuario se inclina por SÍ (ingreso/sim para ser usable), pero prefiere DEFINIRLO más adelante porque el front y el backend se desarrollan en paralelo. Decisión diferida a Application Design; el diseño debe aislar el origen de datos (capa/adaptador) para no atarse ahora.

---

## P5. ¿Cuál debería ser la PRIMERA rebanada de extremo a extremo (walking skeleton) que demuestre que todo funciona?

A. Login → registrar 1 planta → asignarle 1 sensor → ver su humedad/luz en pantalla
B. Login → ver un dashboard con datos (aunque sean simulados)
C. Solo el flujo de cuenta (registro/login) primero, luego lo demás
D. Recomiéndame la rebanada de menor riesgo
X. Other (especificar)

[Answer]: A — Login → registrar 1 planta → asignarle 1 sensor → ver su humedad en pantalla. Este es el walking skeleton.

---

## P6. ¿Hay algo que quieras dejar EXPLÍCITAMENTE fuera del MVP (además del indicador de "salud")? (elige todas las que apliquen)

A. Multiusuario / compartir con otras personas
B. Notificaciones push (si no lo elegiste en P3)
C. Histórico/gráficas de tendencias a lo largo del tiempo
D. Configurar umbrales personalizados por planta/tipo
E. Nada más — con lo anterior basta
X. Other (especificar)

[Answer]: A, D — Fuera del MVP: Multiusuario/compartir y Umbrales personalizados (el MVP usa umbrales fijos). Histórico/tendencias (C) NO se marcó como fuera → queda como "Could" no priorizado. Se suma a lo ya fuera: indicador de "salud" y variable de LUZ.
