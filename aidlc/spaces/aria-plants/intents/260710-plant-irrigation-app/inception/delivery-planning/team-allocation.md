# Team Allocation — Aria Plants (Android MVP)

> Etapa 2.8 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Asignación Bolt→persona. Deriva de `bolt-plan.md`, `unit-of-work.md` y las
> prácticas afirmadas de `team.md` (equipo real de 2 personas: senior/líder técnico
> + junior). NOTA: aunque el scope `mvp` omite Team Formation (1.5) y su default es
> "todos los Bolts = aidlc-developer-agent (AI)", `team.md` afirma un **equipo humano
> real**; se honra la práctica afirmada y se asigna por persona (Q4=A).

## Reparto por experiencia (regla de `team.md`)

- El **senior** toma el **walking skeleton** (Bolt 1) y los Bolts **pesados o de
  integración** (modelo de datos, y cualquier Bolt del que dependan otros).
- El **junior** recibe Bolts **ligeros, autocontenidos y de baja dependencia**, y
  van **gated** (revisión del senior antes de integrar a `main`) como mentoría.

## Asignación

| Bolt | Unidades | Dueño | Modo de gate | Justificación |
|------|----------|-------|--------------|---------------|
| 1 · Walking Skeleton | foundation, auth (+ slice) | **Senior** (solo) | **Gated** (siempre) | Prueba integración crítica (Auth, RLS, render, polling); base de todo |
| 2 · Catálogo completo | zones, plants | **Senior** | Por modo de autonomía | Modelo de datos + cascade + zona-primero (pesado, del que dependen otros) |
| 3 · Monitoreo completo | sensors, dashboard | **Senior** | Por modo de autonomía | Agregación multi-sensor + polling (integración de datos) |
| 4 · Alertas | alerts | **Senior** | Por modo de autonomía | Integración (tabla alerts, dedup, push); riesgo técnico |
| 5 · Perfil & Ajustes | profile | **Junior** | **Gated** (revisión del senior) | Ligero, autocontenido, baja dependencia (i18n, toggle, logout) |

## Paralelismo (Q3=A)

- Pipeline **serial** para la cadena del senior (Bolt 1 → 2 → 3 → 4), por las
  dependencias del DAG (zones→plants→sensors→dashboard→alerts).
- **Bolt 5 (`profile`)** es independiente en el DAG (solo depende de foundation+auth,
  disponibles tras Bolt 1) → el **junior lo desarrolla en paralelo** a la cadena del
  senior, en su propia rama corta, con revisión del senior antes de integrar.

## Notas

- Es el análogo mínimo de un "Program Board": 2 personas, una cadena principal
  (senior) + un carril paralelo (junior).
- Todos los Bolts se integran con **squash-merge** a `main` (una rama corta por
  Bolt, `team.md`).
- Los Bolts del junior van gated **siempre**, sin importar el modo de autonomía que
  se elija tras el walking skeleton.
