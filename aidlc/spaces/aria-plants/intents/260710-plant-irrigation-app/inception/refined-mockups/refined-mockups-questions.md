# Refined Mockups & UX — Preguntas de Clarificación

> Etapa 2.5 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Fuente de verdad para la traza de decisiones. Rellena cada `[Answer]:`.
> Cada pregunta ofrece opciones A–E según aplique + `X. Other (please specify)`.
> Deriva de `wireframes.md`, `user-flow.md` (1.6), `stories.md`, `requirements.md`
> y `team-practices.md`. Los rough mockups ya fijan estructura y estilo (dark-first,
> ES/EN, tabs, solo humedad); estas preguntas resuelven lo que queda abierto para
> refinar a media/alta fidelidad.

---

## Q1 — Enforcement de la regla "zona-primero" en la UI

La regla `Decided` (zona-primero) exige que exista ≥1 zona antes de crear una
planta, sin zona por defecto implícita. Los rough mockups permiten "crear zona
inline" desde el formulario de planta. ¿Cómo lo enforzamos en la UI?

- A. **Estricta con creación inline**: el formulario de planta exige elegir zona;
     si no hay zonas, el selector ofrece "crear zona" inline (la primera zona se
     crea dentro del flujo). Cumple zona-primero sin bloquear el onboarding (como en los wireframes).
- B. **Estricta con redirección**: si no hay zonas, al intentar crear planta la app
     redirige primero a "crear zona" (pantalla separada) y luego vuelve al formulario.
- C. **Ambas rutas** disponibles (inline + tab Zonas); el usuario elige.
- X. Other (please specify)

[Answer]: A

## Q2 — Patrón de interacción para crear/editar

¿Qué patrón usamos para los formularios (crear/editar planta, zona, asignar sensor)?

- A. **Pantallas completas** (full-screen con botón atrás) para todos los formularios
     — consistente con los wireframes actuales; simple y claro en móvil.
- B. **Híbrido**: bottom sheets/modales para acciones rápidas (crear zona inline,
     asignar sensor) y pantalla completa para formularios grandes (crear/editar planta).
- C. **Modales / bottom sheets** para todo.
- X. Other (please specify)

[Answer]: A

## Q3 — Biblioteca de componentes / design system

Para dark-first, i18n y accesibilidad en Expo/React Native, ¿qué base de UI?

- A. **React Native Paper** (Material Design 3): soporte nativo de tema oscuro,
     componentes accesibles, theming por tokens. Rápido y consistente. (recomendado)
- B. **Componentes propios** sobre primitivos de RN + un set de design tokens
     propio (máximo control del look minimalista; más trabajo de implementación).
- C. **Otra librería** (Tamagui, gluestack-ui, NativeBase, etc.) — indícala.
- X. Other (please specify)

[Answer]: A

## Q4 — Nivel de accesibilidad objetivo

¿Qué objetivo de accesibilidad fijamos para el `accessibility-checklist.md`?

- A. **WCAG 2.1 AA** (contraste ≥4.5:1, labels en todos los controles, orden de
     foco, touch targets ≥44dp, no depender solo del color) — estándar razonable para MVP.
- B. **WCAG 2.1 A** (mínimo).
- C. **AA + extras**: pruebas con lector de pantalla **TalkBack** y soporte de
     escalado de fuente del sistema.
- X. Other (please specify)

[Answer]: A

## Q5 — UX del permiso de notificaciones push

Para las alertas push (HU-F3, Should). ¿Cuándo/cómo pedimos el permiso de Android?

- A. **Contextual con priming**: explicar el beneficio antes de pedir el permiso
     del sistema, disparado al activar el toggle de push en Perfil o tras crear la
     primera planta con sensor.
- B. **Al primer arranque** tras el login.
- C. **Diferido**: no pedir hasta que ocurra la primera alerta de humedad baja.
- X. Other (please specify)

[Answer]: B

## Q6 — Tratamiento de "sin dato" y estados de lectura

`RF-E2` NO marca "sin dato" por antigüedad (siempre muestra la última lectura
conocida); el user-flow menciona "sin lectura de sensor -> sin dato". ¿Cómo lo
mostramos sin contradecir el requisito?

- A. **"Sin dato" solo cuando el sensor nunca reportó** (cero lecturas). Con ≥1
     lectura, se muestra la última conocida **sin** marca de antigüedad (fiel a RF-E2).
- B. Mostrar "sin dato" también si la última lectura supera cierta antigüedad
     (esto **contradice** RF-E2 y requeriría revisar el requisito).
- C. Siempre mostrar la última lectura conocida; "sin dato" es solo un placeholder
     inicial hasta que llega la primera lectura.
- X. Other (please specify)

[Answer]: A

## Q7 — Fidelidad y formato de los mockups refinados

¿Qué entregamos como mockups de media/alta fidelidad en este repo markdown?

- A. **Wireframes ASCII refinados** (con estados detallados y tokens) + specs de
     componentes en texto (usando la plantilla del design-agent) + design tokens.
     Todo en markdown, sin herramienta externa. (recomendado, coherente con el repo)
- B. **A + mockups visuales en Figma** para las pantallas clave (hay MCP de Figma
     disponible); se enlazan desde `mockups.md`.
- C. **Solo** especificación de componentes + design tokens (sin re-dibujar wireframes).
- X. Other (please specify)

[Answer]: A
