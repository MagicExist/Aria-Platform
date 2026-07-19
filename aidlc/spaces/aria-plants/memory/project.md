# Project-Level Rules

> Project-specific overrides and corrections. Overrides aidlc-team.md
> and aidlc-org.md. Populated by practices-discovery and the
> self-learning loop.
>
> Use sparingly: most teams don't need a project layer. Reach for it
> only when this specific project deviates from team-wide practice in a
> stable, durable way (e.g., "this monorepo project rebases even though
> our team default is squash"; "this legacy project skips the test
> floor because the existing suite is unsalvageable and we accept
> that").

## Way of Working

<!-- Project-specific override. Example: -->
<!-- This monorepo project rebases instead of squash-merging because -->
<!-- the per-package commit history is the audit trail we depend on -->
<!-- for partial-rollback decisions. Override applies to this project -->
<!-- only. -->

## Walking Skeleton

<!-- Project-specific override. Example: -->
<!-- This project skips the walking skeleton because we're rewriting -->
<!-- an existing service in-place — there's no greenfield bootstrap -->
<!-- to gate. -->

## Testing Posture

<!-- Project-specific override. -->

## Deployment

<!-- Project-specific override. -->

## Code Style

<!-- Project-specific override. -->

## Tech Stack

<!-- Technology choices locked for this project. -->

- El backend es **Supabase** (gestionado): Postgres + Auth + RLS + API auto-generada. La app (Expo/React Native + TypeScript) habla directo con Supabase vía su cliente JS. Este repositorio contiene ÚNICAMENTE la app. La ingesta de lecturas de sensores HACIA Supabase (sensores -> Supabase) queda fuera del alcance de este repo; la app solo LEE los datos (por polling en el MVP). (learned 2026-07-10) <!-- cid:feasibility:c1 -->
- Este proyecto NO usa AWS, aunque el framework AI-DLC lo asume por defecto (Bedrock, CDK, Well-Architected). Stack real: Expo + TypeScript + Supabase (BaaS). Application Design e Infrastructure Design NO deben introducir supuestos de AWS. (learned 2026-07-10) <!-- cid:feasibility:c3 -->
- **Solo Android.** Plataforma única: Android (React Native/Expo), distribuida como APK. Web e iOS quedan FUERA de alcance (decidido en gate de feasibility, 2026-07-10; iOS no verificable sin Mac, web no requerida). No generar targets web/iOS. <!-- cid:feasibility:platform-android-only -->
## Decided

<!-- Decisions made in earlier stages that should not be re-asked. -->
<!-- Format: DECIDED: [decision] (Stage [slug], [date]) -->

- Regla zona-primero: debe existir al menos una zona antes de poder crear plantas; cada planta pertenece a exactamente una zona; no hay zona por defecto implícita. La foto de planta es feature obligatoria (Must) pero opcional al crear. Aplica a refined-mockups, application-design, delivery-planning y code-generation (el walking skeleton/Bolt 1 debe incluir 'crear zona' antes de 'crear planta'). (learned 2026-07-19) <!-- cid:user-stories:c5 -->
- Agregación multi-sensor a nivel de planta (worst-case): el estado de una planta = el peor de los estados de sus sensores (BAJA si algún sensor está BAJA); el valor de humedad mostrado = la lectura más baja entre los sensores que han reportado; 'sin dato' solo si TODOS los sensores nunca reportaron; la alerta (RF-F1) se dispara si CUALQUIER sensor cruza el umbral. Es intención de diseño pendiente de confirmar en Application Design (OQ-refined-1). Aplica a application-design, functional-design y code-generation. (learned 2026-07-19) <!-- cid:refined-mockups:c3 -->
- Foto de planta SOLO local en el dispositivo (Q7=C, ADR-007): el registro de la planta guarda un URI local, no se usa Supabase Storage. Consecuencia: la foto no se sincroniza entre dispositivos, se pierde al reinstalar/cambiar de dispositivo y no queda cubierta por RLS ni backup. Simplificación MVP, reversible a Supabase Storage post-MVP (solo cambia PlantService.setPhoto y el tipo del campo). Aplica a functional-design y code-generation. (learned 2026-07-19) <!-- cid:application-design:c4 -->
## Scope Overrides

<!-- Custom scope rules for this project. -->

## Forbidden

<!-- Populated by practices-discovery affirmation gate. -->
<!-- Format: NEVER [behavior] (affirmed [date]) -->
<!-- Example: NEVER throw exceptions across service layer boundaries (affirmed 2026-05-17) -->

NEVER provision staging or production server environments for the MVP (backend is managed Supabase; no own server infra) (affirmed 2026-07-10)
NEVER expose the Supabase service key in the app client (only the public anon key belongs in the client) (affirmed 2026-07-10)
## Mandated

<!-- Populated by practices-discovery affirmation gate. -->
<!-- Format: ALWAYS [behavior] (affirmed [date]) -->
<!-- Example: ALWAYS use Result<T,E> for fallible operations in service layer (affirmed 2026-05-17) -->

ALWAYS squash-merge feature branches into `main` (one commit per branch) (affirmed 2026-07-10)
ALWAYS run the walking skeleton as a gated, solo Bolt 1 before any other Construction Bolt (affirmed 2026-07-10)
ALWAYS write tests alongside code, keep ~80% line coverage, and require CI green before merge (affirmed 2026-07-10)
ALWAYS use ESLint + Prettier + TypeScript strict for the app codebase (affirmed 2026-07-10)
ALWAYS distribute the app as an Android APK (EAS or local build) (affirmed 2026-07-10)
## Corrections

<!-- Project-specific corrections from human feedback. -->
<!-- Format: NEVER/ALWAYS [behavior] (learned [date]) -->

## UI Conventions
- Estilo visual: **modo oscuro como tema principal** (dark-first; el tema claro es post-MVP), estilo limpio y minimalista, marca **"Aria Plants"**. El verde/naturaleza solo como acento sutil opcional, no como tema base. (learned 2026-07-10) <!-- cid:rough-mockups:c2 -->
- **i18n desde el inicio**: la app soporta español e inglés con selector de idioma. Español es el idioma de trabajo por defecto. Todo texto de UI debe pasar por i18n (sin strings hardcodeados). (learned 2026-07-10) <!-- cid:rough-mockups:c3 -->
