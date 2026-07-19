# Team Practices — Aria Plants

> Etapa 2.2 · Fase Inception · Greenfield · Fecha: 2026-07-10
> Prácticas afirmadas en la entrevista de practices-discovery. Voz de equipo.
> Al aprobar, estas 5 secciones se promueven a `team.md`.

## Way of Working

Usamos desarrollo trunk-based: el trabajo se integra a `main` mediante ramas de
feature cortas (1–2 días) y se hace **squash-merge** (cada rama = un commit en
`main`), manteniendo un historial lineal. Es un proyecto personal en solitario,
pero mantenemos la disciplina de ramas cortas por hábito y para facilitar el
escalado futuro.

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
