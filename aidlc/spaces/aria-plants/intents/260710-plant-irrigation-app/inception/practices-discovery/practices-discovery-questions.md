# Practices Discovery — Entrevista de Prácticas

> Etapa 2.2 · Fase Inception · Greenfield (sin código que escanear)
> Cinco áreas de práctica. Los defaults vienen de `org.md`; puedes elegir una
> variante más ligera acorde a un proyecto personal. Lo que afirmes se promueve
> a `team.md` / `project.md`.
> Responde escribiendo la(s) letra(s) en `[Answer]:`.

---

## P1. Way of Working (ramas y merge)

A. Trunk-based: ramas de feature cortas → `main`, con squash-merge (default org)
B. Commit directo a `main` (proyecto personal en solitario, sin PRs) — más simple
C. Trunk-based pero con merge normal (sin squash)
D. Recomiéndame
X. Other (especificar)

[Answer]: A — Trunk-based con ramas de feature cortas → main y squash-merge (default org).

---

## P2. Walking Skeleton (primer Bolt en Construcción)

A. Sí — correr el walking skeleton primero, con Bolt 1 en solitario y con gate (default greenfield)
B. No usar la ceremonia de walking skeleton
C. Depende del alcance
X. Other (especificar)

[Answer]: A — Sí, correr el walking skeleton primero (Bolt 1 solo y con gate). Skeleton definido: login→planta→sensor→humedad.

---

## P3. Testing Posture

A. Pruebas junto al código, cobertura ~80%, CI antes de merge (default org para MVP)
B. Pruebas de los caminos críticos, sin piso estricto de cobertura (más ligero; apto para aprendizaje)
C. Mínimo: 1 prueba por requisito clave; sin cobertura obligatoria
D. Recomiéndame
X. Other (especificar)

[Answer]: A — Pruebas junto al código, cobertura ~80%, CI antes de merge (default org).

---

## P4. Deployment

A. Deploy on merge a staging + producción con aprobación manual (default org)
B. Build de APK (EAS/local) e instalación manual; sin entornos staging/prod (encaja con este proyecto)
C. Recomiéndame
X. Other (especificar)

[Answer]: B — Build de APK (EAS/local) e instalación manual; sin entornos staging/prod.

---

## P5. Code Style

A. Deferir a config del proyecto: **ESLint + Prettier + TypeScript strict** (estándar Expo/RN)
B. Solo Prettier (formato), sin linter estricto
C. Recomiéndame
X. Other (especificar)

[Answer]: A — ESLint + Prettier + TypeScript strict (estándar Expo/RN).
