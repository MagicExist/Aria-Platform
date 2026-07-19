# Application Design — Preguntas de Diseño

> Etapa 2.6 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Fuente de verdad para la traza de decisiones. Rellena cada `[Answer]:`.
> Cada pregunta ofrece opciones A–E según aplique + `X. Other (please specify)`.
> Deriva de `requirements.md`, `stories.md`, `team-practices.md`, los refined
> mockups (2.5) y las reglas de `project.md` (Supabase, no AWS, zona-primero,
> agregación multi-sensor worst-case). Resuelve las open questions abiertas.

---

## Q1 — Estructura / organización del código (arquitectura de la app)

¿Cómo organizamos el código de la app Expo/React Native + TS?

- A. **Por feature** (carpetas `features/auth`, `features/plants`, `features/zones`,
     `features/sensors`, `features/dashboard`, `features/alerts`) + capas compartidas
     (`ui/`, `lib/`, `services/`, `i18n/`). Escala bien y aísla dominios. (recomendado)
- B. **Por capa** clásica (`screens/`, `components/`, `services/`, `models/`, `hooks/`).
- C. Otra (indícala).
- X. Other (please specify)

[Answer]: A

## Q2 — Capa de acceso a datos sobre Supabase

¿Cómo hablan los componentes con Supabase?

- A. **Capa de servicios/repositorio por dominio** (`AuthService`, `PlantService`,
     `ZoneService`, `SensorService`, `ReadingsService`) que envuelve el cliente
     Supabase; los componentes NO llaman a Supabase directo. Aísla el contrato de
     sensores (C-4) tras un adaptador. (recomendado)
- B. **Llamadas directas** al cliente Supabase desde hooks/componentes (menos capas,
     más acoplamiento).
- C. Otra (indícala).
- X. Other (please specify)

[Answer]: A

## Q3 — Estado de servidor, fetching y polling

¿Con qué manejamos fetching, caché, estados (loading/error/stale) y el polling ~5 min (RF-E2)?

- A. **React Query (TanStack Query)**: caché + `refetchInterval` ~5 min + estados
     loading/error de fábrica; estado local con hooks/Context. (recomendado)
- B. **Redux Toolkit + RTK Query**.
- C. **Zustand** + fetch manual.
- D. Solo **hooks/Context propios** + fetch manual.
- X. Other (please specify)

[Answer]: A

## Q4 — Origen de lecturas de sensores mientras no exista el contrato (C-4 / OQ-1 / S-2)

La ingesta sensores→Supabase es externa a este repo; la app solo LEE. El contrato aún no existe.

- A. **Adaptador `ReadingsSource`** con 2 implementaciones detrás de una interfaz:
     (1) lectura real desde una tabla Supabase (p. ej. `readings`) por polling, y
     (2) un **simulador** local para desarrollo; se elige por config. La app depende
     de la interfaz, no del origen. (recomendado)
- B. Solo **lectura desde Supabase** (tabla `readings` asumida), sin simulador.
- C. Solo **simulador** por ahora (integración real más tarde).
- X. Other (please specify)

[Answer]: A

## Q5 — Dónde se computa la agregación multi-sensor (OQ-refined-1)

Regla ya decidida: estado planta = peor de sus sensores; valor = lectura más baja
(persistida en `project.md`). ¿Dónde se calcula?

- A. **En el cliente** (en `ReadingsService`/selector) a partir de las últimas
     lecturas por sensor. Simple, sin depender de features de DB. (recomendado MVP)
- B. **En Postgres** (vista o RPC de Supabase) que devuelve el estado agregado por
     planta. Menos cómputo en cliente, más lógica en DB.
- C. Híbrido (indícalo).
- X. Other (please specify)

[Answer]: A

## Q6 — Borrado en cascada (OQ-3): eliminar planta / zona

¿Qué pasa con lo asociado al borrar?

- A. Eliminar **planta** → borra en **cascada** sus sensores y sus lecturas;
     eliminar **zona** → **bloqueada** si tiene plantas (mover/eliminar primero).
     Consistente con zona-primero. (recomendado)
- B. Eliminar **planta** → **desasigna** sensores (los deja reasignables); eliminar
     **zona** → **reasigna** sus plantas a otra zona elegida.
- C. Otra combinación (indícala).
- X. Other (please specify)

[Answer]: A

## Q7 — Almacenamiento de la foto de planta (OQ-2)

RF-B1: foto opcional. ¿Dónde vive?

- A. **Supabase Storage** (bucket **privado** con RLS por usuario); la planta guarda
     la clave/URL del objeto. Integra con Auth/RLS. (recomendado)
- B. Guardar solo una **URL externa** (sin subir archivo).
- C. Guardar la imagen **solo local** en el dispositivo.
- X. Other (please specify)

[Answer]: C
