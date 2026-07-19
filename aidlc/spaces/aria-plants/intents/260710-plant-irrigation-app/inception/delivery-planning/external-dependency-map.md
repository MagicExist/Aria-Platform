# External Dependency Map — Aria Plants (Android MVP)

> Etapa 2.8 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Ítems externos que condicionan los Bolts de `bolt-plan.md`. Deriva de
> `requirements.md` (constraints/assumptions), `components.md`, `decisions.md` y
> `unit-of-work.md`. Confirmado en Q6=A. El proyecto es mayormente autocontenido
> (app cliente + Supabase gestionado); sin hand-offs de otros equipos.

## Ítems externos

| Dependencia | Tipo | Dueño | Lead time | Bolt(s) que bloquea | Mitigación / workaround |
|-------------|------|-------|-----------|---------------------|--------------------------|
| **Proyecto Supabase** (Postgres + Auth + RLS) | Servicio gestionado | Equipo (cuenta propia) | Disponible | Bolt 1 (y todos) | Ya disponible; crear tablas + policies RLS como parte del Bolt 1 |
| **Contrato de datos de sensores** (esquema de `readings`, ingesta) | Contrato externo (C-4) | Equipo de sensores (fuera de este repo) | **Indefinido / pendiente** | Bolt 1 (lectura), Bolt 3 (monitoreo) | **Adaptador `ReadingsSource` + simulador** (ADR-004, S-2): la app se desarrolla/testea con lecturas simuladas; al llegar el contrato, solo cambia `SupabaseReadingsSource` |
| **Mecanismo de push** (expo-notifications + posible función Supabase-side) | Servicio/plataforma | Equipo (a definir) | A definir en NFR/Infra Design (OQ-4, S-3) | Bolt 4 (push HU-F3) | **Diferido** (ADR-011); las alertas **in-app** (HU-F2) no dependen de push; push es Should |
| **Build/distribución del APK** (EAS o build local) | Herramienta de build | Equipo | Configurable | CI Pipeline (3.7) / entrega | EAS con cuenta Expo, o build local; sin tiendas en el MVP (`team-practices.md`) |

## Observaciones

- **Sin dependencias de otros equipos internos ni aprobaciones externas**: es un
  proyecto de 2 personas sobre BaaS gestionado (Supabase); no hay infra propia que
  aprovisionar (`project.md` Forbidden: no staging/prod server envs).
- **Única dependencia con lead time real e indefinido**: el **contrato de sensores**
  (C-4). Está **aislado por diseño** (adaptador + simulador), por lo que **no
  bloquea** ningún Bolt: el MVP funciona end-to-end con el simulador y se conecta al
  origen real cuando exista, sin refactor de la app.
- **Push** es la otra pieza "externa" (plataforma de notificaciones): diferida y
  marcada como Should, con degradación limpia a in-app.

## Resumen de bloqueo

Ningún Bolt queda **bloqueado** por una dependencia externa gracias a las
mitigaciones (simulador de lecturas; push diferido/Should). El Bolt 1 solo necesita
el proyecto Supabase, que ya está disponible.
