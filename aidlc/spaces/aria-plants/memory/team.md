# Team-Level Rules

> This team's affirmed practices and corrections. Overrides aidlc-org.md.
> Populated by practices-discovery affirmation gate. Edit at the gate,
> not directly.

## Way of Working

Usamos desarrollo trunk-based: el trabajo se integra a `main` mediante ramas de
feature cortas (1–2 días) y se hace **squash-merge** (cada rama = un commit en
`main`), manteniendo un historial lineal. **Somos un equipo de dos personas** (no
un proyecto en solitario): un desarrollador senior/líder técnico con más
experiencia y un compañero junior que se está iniciando. Mantenemos la disciplina
de ramas cortas para permitir el trabajo en paralelo y la revisión de código
entre ambos. (affirmed 2026-07-11)

**División del trabajo por experiencia (input para `delivery-planning`):** dado el
desnivel de experiencia, los Bolts se dimensionan de forma desigual. El senior
toma el walking skeleton (Bolt 1) y los Bolts pesados o de integración (auth,
modelo de datos, y cualquier Bolt del que dependan otros). El junior recibe Bolts
ligeros, autocontenidos y de baja dependencia (p. ej. una sola pantalla, el
trabajo de i18n ES/EN, un widget de dashboard aislado), y sus Bolts van **gated**
(revisión del senior antes de integrar a `main`) como apoyo y mentoría.
(affirmed 2026-07-11)

## Walking Skeleton

Corremos el **walking skeleton primero** por ser greenfield: el Bolt 1 de
Construcción implementa la rebanada de extremo a extremo **login → registrar
planta → asignar sensor → ver humedad**, en solitario y con gate de aprobación,
antes de construir el resto de los Bolts. Prueba temprano los puntos de
integración (Supabase Auth, Postgres, render en Android).

## Testing Posture

Las pruebas son un entregable de primera clase: se escriben **junto al código**,
con un piso de **~80% de cobertura de líneas**, y la CI debe estar en verde
antes de integrar a `main`.

## Deployment

No usamos entornos de servidor staging/producción. La app se distribuye como
**APK de Android** construido con EAS (o build local) e instalado manualmente en
el dispositivo; no hay publicación en tiendas en el MVP. El backend es Supabase
gestionado (no hay infraestructura propia que desplegar). *(Esto sustituye
deliberadamente el default de organización de "deploy a staging + producción".)*

## Code Style

Deferimos a la configuración del proyecto: **ESLint + Prettier + TypeScript en
modo strict**, según el estándar del ecosistema Expo/React Native. El linter y el
formateador corren en CI y su fallo bloquea la integración.
## Forbidden

<!-- Team-specific forbidden patterns -->

## Mandated

<!-- Team-specific mandates -->

## Corrections

<!-- Self-learning loop appends here. -->
