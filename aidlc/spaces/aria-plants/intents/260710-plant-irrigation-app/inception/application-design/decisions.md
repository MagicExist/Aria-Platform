# Application Design — Architecture Decision Records (ADRs)

> Etapa 2.6 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Decisiones de arquitectura derivadas de `requirements.md`, `stories.md`,
> `team-practices.md`, los refined mockups (2.5) y las respuestas
> `application-design-questions.md` (Q1–Q7). Stack: Expo/RN + TS, Supabase, **sin
> AWS** (`project.md`). Cada ADR: Contexto · Decisión · Consecuencias ·
> Alternativas rechazadas · Reversibilidad.

---

## ADR-001 · Organización del código por feature (Q1=A)
- **Contexto**: app de varios dominios (auth, plantas, zonas, sensores, dashboard, alertas); equipo de 2 (senior+junior) con Bolts dimensionados por experiencia.
- **Decisión**: estructura **por feature** (`features/<dominio>`) + capas compartidas (`ui`, `lib`, `services`, `i18n`, `config`, `navigation`).
- **Consecuencias**: dominios aislados → Bolts más independientes (bueno para el reparto senior/junior); un poco más de boilerplate de carpetas.
- **Alternativas rechazadas**: por capa clásica (screens/components/services) — mezcla dominios y dificulta aislar un Bolt.
- **Reversibilidad**: media (mover archivos); barata si se hace temprano.

## ADR-002 · Capa de servicios/repositorio sobre Supabase (Q2=A)
- **Contexto**: la UI no debe acoplarse al SDK de Supabase; el contrato de sensores no existe (C-4).
- **Decisión**: servicios de dominio (`Auth/Zone/Plant/Sensor/Readings/Alert`) envuelven el cliente; solo `lib/supabase` importa el SDK.
- **Consecuencias**: testeable (se mockean servicios), cambiar backend afecta una capa; una indirección extra.
- **Alternativas rechazadas**: llamadas directas a Supabase desde hooks/componentes — rápido pero acopla la UI al SDK y dispersa la lógica.
- **Reversibilidad**: alta al inicio.

## ADR-003 · React Query para estado de servidor + polling (Q3=A)
- **Contexto**: humedad por polling ~5 min (RF-E2, NFR-Frescura); necesidad de estados loading/empty/error/stale consistentes (refined mockups).
- **Decisión**: **TanStack React Query** para fetching/caché/polling (`refetchInterval` ~5 min); estado local con hooks/Context.
- **Consecuencias**: maneja frescura, reintentos y estados por diseño; invalidación de caché al mutar y reset al cambiar de sesión (refuerza HU-A4); una dependencia más.
- **Alternativas rechazadas**: RTK Query (más peso/boilerplate para el tamaño del MVP); Zustand/hooks propios (habría que reimplementar caché/polling/estados).
- **Reversibilidad**: media.

## ADR-004 · Adaptador `ReadingsSource` para el origen de lecturas (Q4=A)
- **Contexto**: la ingesta sensores→Supabase es externa y el contrato no existe (C-2, C-4, OQ-1, S-2); no bloquear el desarrollo de la app.
- **Decisión**: interfaz `ReadingsSource` con `SupabaseReadingsSource` (tabla `readings` por polling) y `SimulatedReadingsSource` (dev), elegidas por `config`.
- **Consecuencias**: la app se desarrolla/testea con el simulador; al llegar el contrato real solo cambia la implementación Supabase; leve indirección.
- **Alternativas rechazadas**: asumir la tabla real sin simulador (bloquea dev y tests deterministas); solo simulador (no ejercita la integración real).
- **Reversibilidad**: alta (es un punto de extensión por diseño).

## ADR-005 · Agregación multi-sensor en el cliente (Q5=A)
- **Contexto**: una planta puede tener varios sensores; se decidió worst-case (`project.md` Decided, OQ-refined-1).
- **Decisión**: `computePlantStatus` (función pura, cliente) calcula estado/valor por planta a partir de las últimas lecturas por sensor; 'no-data' solo si ningún sensor reportó.
- **Consecuencias**: simple, testeable, sin features de DB; si el volumen de sensores creciera mucho, convendría mover a Postgres (no es el caso MVP).
- **Alternativas rechazadas**: vista/RPC en Postgres (más lógica en DB, más difícil de testear en MVP); híbrido (complejidad prematura).
- **Reversibilidad**: alta (función aislada; se puede sustituir por una vista después).
- **Nota**: intención de diseño pendiente de confirmar en Functional Design; la regla ya está persistida como práctica de proyecto.

## ADR-006 · Borrado en cascada + zona no borrable con plantas (Q6=A)
- **Contexto**: integridad al eliminar; regla zona-primero (cada planta pertenece a una zona).
- **Decisión**: FK con `ON DELETE CASCADE` planta→sensores→lecturas; borrar **zona** con plantas se **bloquea** (ConflictError; el usuario mueve/elimina plantas antes).
- **Consecuencias**: evita huérfanos y zonas que romperían zona-primero; borrar planta es destructivo (se confirma en UI, ConfirmDialog).
- **Alternativas rechazadas**: desasignar sensores al borrar planta (deja sensores huérfanos sin dueño) y reasignar plantas al borrar zona (comportamiento sorpresivo).
- **Reversibilidad**: media (cambia el esquema/constraints DB).

## ADR-007 · Foto de planta SOLO local en el dispositivo (Q7=C)
- **Contexto**: RF-B1 foto opcional; el usuario prefirió no configurar almacenamiento remoto en el MVP.
- **Decisión**: la foto se guarda **localmente** (URI en el dispositivo); el registro de la planta almacena esa ruta local. **No** se usa Supabase Storage.
- **Consecuencias (importantes)**:
  - La foto **no se sincroniza** entre dispositivos y **se pierde** al reinstalar o cambiar de teléfono.
  - **No** queda cubierta por RLS ni por backup del backend.
  - Simplifica el MVP (sin bucket/policies de Storage).
- **Alternativas rechazadas**: **Supabase Storage** (bucket privado con RLS; sincroniza y respeta aislamiento — recomendado, pero el usuario lo pospuso); URL externa (requiere hosting propio).
- **Reversibilidad**: **alta** — migrar a Supabase Storage post-MVP solo cambia `PlantService.setPhoto` y el tipo del campo (URI local → clave de objeto); se marcó como mejora post-MVP.

## ADR-008 · Autenticación y aislamiento con Supabase Auth + RLS (sin AWS)
- **Contexto**: RF-A (cuentas, sesión, aislamiento), NFR-Seguridad (solo anon key en cliente); `project.md` prohíbe supuestos AWS y exponer la service key.
- **Decisión**: **Supabase Auth** (email+password) + **RLS** por `auth.uid()` en todas las tablas; el cliente solo lleva la **clave anónima pública**.
- **Consecuencias**: aislamiento garantizado en el backend (defensa en profundidad junto al reset de caché en cliente); dependencia del proveedor Supabase (aceptada, es el stack).
- **Alternativas rechazadas**: backend/identidad propios o AWS Cognito — fuera de alcance y contra `project.md` (no AWS, no infra propia).
- **Reversibilidad**: baja (es el pilar del stack).

## ADR-009 · i18n (ES/EN) y theming dark-first con React Native Paper
- **Contexto**: NFR-Usabilidad (dark-first, i18n, sin strings hardcodeados); design system elegido en 2.5 (Q3=A refined: RN Paper/MD3).
- **Decisión**: **React Native Paper** (MD3, tema oscuro) con tokens centralizados; **i18next**/`expo-localization` con recursos ES/EN, español por defecto.
- **Consecuencias**: tema claro se añade post-MVP sin refactor; disciplina de "sin hardcodeo" verificable por lint (NFR-Calidad).
- **Alternativas rechazadas**: componentes propios (más esfuerzo para equipo de 2); strings embebidos (rompe i18n).
- **Reversibilidad**: media (theming) / alta (recursos i18n).

## ADR-010 · Estado de alertas persistido en Postgres (`alerts`)
- **Contexto**: la evaluación de humedad ocurre por polling (~5 min). HU-F1 exige "una alerta activa por condición" (no re-disparar cada ciclo) y HU-F4 auto-resolución; eso requiere estado que sobreviva entre ciclos de polling y entre sesiones/dispositivos.
- **Decisión**: persistir las alertas en una tabla Postgres **`alerts`** (`id, plantId, sensorId?, firedAt, resolved`, RLS por usuario). `AlertService.evaluateAndNotify` de-duplica contra las alertas activas de esa tabla y marca `resolved` al recuperarse la humedad; `getActiveAlerts` lee de ahí.
- **Consecuencias**: de-dup y estado consistente entre sesiones/dispositivos; una tabla más y su RLS. Habilita que un futuro trigger/Edge Function dispare el push al insertarse una alerta (ver push abajo).
- **Alternativas rechazadas**: derivar alertas solo en memoria por ciclo (re-dispararía en cada poll, rompe HU-F1; sin estado entre sesiones); estado solo local en dispositivo (no sincroniza, incoherente entre dispositivos).
- **Reversibilidad**: media (nueva tabla + RLS).

## ADR-011 · Entrega de push puede requerir una función del lado de Supabase
- **Contexto**: HU-F3 pide push "aunque la app esté cerrada". Un modelo puramente cliente por polling no corre JS con el proceso inactivo, así que no puede entregar push en background.
- **Decisión**: mantener la evaluación in-app por polling para las alertas **in-app** (HU-F2), y **diferir** el mecanismo de push a NFR/Infra Design (OQ-4, S-3), asumiendo que probablemente sea una **Supabase Edge Function** disparada por un trigger de Postgres sobre la tabla `alerts`.
- **Consecuencias**: no bloquea el MVP (in-app funciona); el push queda como pieza gestionada por Supabase, sin servidor propio (coherente con `project.md`, no infra propia / no AWS).
- **Alternativas rechazadas**: prometer push solo-cliente (imposible en background); montar un servidor propio (contra `project.md`).
- **Reversibilidad**: alta (decisión aún abierta; se concreta en NFR/Infra Design).

---

## Resumen de reversibilidad

| ADR | Decisión | Reversibilidad |
|-----|----------|----------------|
| 001 | Estructura por feature | Media/alta |
| 002 | Capa de servicios | Alta |
| 003 | React Query | Media |
| 004 | ReadingsSource adapter | Alta |
| 005 | Agregación en cliente | Alta |
| 006 | Cascade + zona bloqueada | Media |
| 007 | Foto local | **Alta** (migrable a Storage) |
| 008 | Supabase Auth + RLS | Baja (pilar) |
| 009 | i18n + dark-first (RN Paper) | Media/alta |
| 010 | Alertas en tabla Postgres `alerts` | Media |
| 011 | Push vía función Supabase-side (diferido) | Alta |

## Preguntas abiertas remanentes (para etapas siguientes)

- **OQ-4 / S-3**: mecanismo concreto de push (expo-notifications) y permisos → NFR/Infra Design.
- **C-4**: contrato real de sensores → cuando exista, actualizar `SupabaseReadingsSource` (aislado por ADR-004).
- **Desempate `AlertItem.sensorName`** cuando 2+ sensores cruzan a la vez → resolver en Functional Design (usar el de lectura más baja, coherente con ADR-005).
