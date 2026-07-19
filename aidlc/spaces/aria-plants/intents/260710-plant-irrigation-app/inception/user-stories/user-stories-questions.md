# User Stories — Plan y Preguntas

> Etapa 2.4 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-14
> Fuente de verdad para la traza de decisiones. Rellena cada `[Answer]:`.
> Cada pregunta ofrece opciones A–E según aplique + `X. Other (please specify)`.

## Plan (resumen)

- **Formato de historia**: "Como [persona], quiero [objetivo], para [beneficio]" +
  criterios de aceptación en **Given/When/Then (BDD)** (guardrail de inception) +
  prioridad **MoSCoW** + notas **INVEST**.
- **Trazabilidad**: cada historia referencia su(s) requisito(s) RF-A…RF-F / NFR.
- **Prioridad**: MoSCoW por historia; la frontera formal del MVP se decide en
  Delivery Planning (2.8) — las prioridades aquí la informan.
- **Personas**: definidas en `personas.md`; historias en `stories.md`.

Las preguntas de abajo resuelven lo que `requirements.md` dejó abierto para el
modelado de historias (personas, granularidad, frontera del MVP). El resto ya está
fijado por RF-A…RF-F.

---

## Q1 — Alcance de personas

`requirements.md` (S-1) asume **un único usuario real al inicio** (el propio autor,
cuidador doméstico), con diseño que puede crecer. ¿Qué personas modelamos?

- A. **Una sola persona**: "El Cuidador" (dueño-hobbyista doméstico). Simple y fiel al MVP.
- B. Una principal ("El Cuidador") + **una secundaria "de futuro"** (p. ej. alguien con
     muchas plantas / mini-huerto urbano) marcada como no-MVP, para guiar decisiones de escalado.
- C. Dos personas activas desde el MVP (p. ej. "Cuidador casual" y "Cuidador metódico"
     con distinta tolerancia a la gestión manual).
- X. Other (please specify)

[Answer]:A

## Q2 — Enfoque de desglose (breakdown)

¿Cómo agrupamos las historias?

- A. **Por grupo de requisito / feature** (Cuentas, Plantas, Sensores, Zonas, Dashboard, Alertas)
     — se alinea 1:1 con RF-A…RF-F y con los Bolts.
- B. Por **workflow / recorrido de usuario** (p. ej. "onboarding", "día a día de monitoreo", "responder a una alerta").
- C. Por **épica** (agrupaciones mayores que cruzan features).
- D. Híbrido: épicas que contienen historias por feature.
- X. Other (please specify)

[Answer]:A

## Q3 — Granularidad de las historias CRUD

Para plantas/zonas/sensores hay operaciones CRUD (crear/ver/editar/eliminar).

- A. **Una historia por operación** (crear planta, editar planta, eliminar planta, …)
     — más historias, máxima trazabilidad y testabilidad INVEST.
- B. **Una historia "gestionar X" por entidad** (gestionar plantas) con criterios de
     aceptación que cubren todas las operaciones — menos historias, más gruesas.
- C. Mixto: separar **crear** y **listar/ver** (valor de cara al usuario claro) pero
     agrupar **editar+eliminar** como "mantener".
- X. Other (please specify)

[Answer]:A

## Q4 — Frontera MVP: entrega de alertas (Must vs Should)

RF-F2 pide alerta **in-app** (lista de "alertas recientes") **y** **push** al cruzar
el umbral. Para la frontera del MVP (informa Delivery Planning):

- A. **Ambas son Must**: lista in-app + notificación push en el MVP.
- B. **In-app es Must; push es Should** (push puede caer si el mecanismo Expo/permite
     Android añade fricción). El walking skeleton no depende de push.
- C. **Push es Must; lista in-app es Should**.
- X. Other (please specify)

[Answer]:B

## Q5 — Frontera MVP: foto y zonas

RF-B1 hace la **foto** de planta opcional; RF-D exige **≥1 zona**. Para MoSCoW:

- A. Foto = **Could**, Zonas = **Must** (crear/asignar zona es parte del núcleo).
- B. Foto = **Should**, Zonas = **Must**.
- C. Foto = **Could**, Zonas = **Should** (una "zona por defecto" implícita basta en el MVP;
     crear/gestionar zonas se puede posponer).
- X. Other (please specify)

[Answer]: La foto como feature es must, pero su funcionalidad a la hora de crear una planta es opcional, las zonas como feature es must como funcionalidad es obligatorio crear una zona para poder crear plantas

## Q6 — Umbral de humedad por defecto

RF-F1 fija el umbral **solo-mínimo** con **valor por defecto 30%** (constante de la
app, no editable por el usuario en el MVP; ajustable en este gate).

- A. **Mantener 30%** como constante del MVP.
- B. Otro valor por defecto (indícalo).
- C. Mantener 30% **pero** dejar registrado que sea editable en config post-MVP.
- X. Other (please specify)

[Answer]: A
