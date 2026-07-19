# Feasibility & Constraints — Preguntas de Clarificación

> Etapa 1.3 · Fase Ideación · Alcance: MVP (profundidad Estándar)
> Enfoque: viabilidad técnica, restricciones e integraciones. Aquí recogemos
> señales de tecnología/habilidad como CONSTRAINTS; la decisión de arquitectura
> real se toma después en Application Design.
> Responde escribiendo la(s) letra(s) en `[Answer]:`.

---

## P1. Enfoque multiplataforma (web + Android + iOS)

¿Cómo imaginas construir las 3 plataformas?

A. Un solo código base con un framework cross-platform (una base para web + móvil)
B. Móvil con un framework cross-platform + web por separado
C. Proyectos separados por plataforma (nativo cada uno)
D. No lo sé aún — quiero la recomendación del arquitecto
X. Other (especificar)

[Answer]: A — **React + Expo (React Native)**. REVISADO en gate de feasibility (2026-07-10): alcance reducido a **solo Android**; se elimina web e iOS. Expo/RN con target Android únicamente.

---

## P2. Preferencia de tecnología / lenguaje (es un proyecto de aprendizaje)

Como el objetivo es aprender, ¿hay algún lenguaje/tecnología que YA conozcas o que QUIERAS usar/aprender para esto? (elige todas las que apliquen)

A. JavaScript / TypeScript (React / React Native / Expo, etc.)
B. Dart / Flutter
C. Ya sé algo de web (HTML/CSS/JS) pero no móvil
D. Sin preferencia — recomiéndame lo más adecuado para web+Android+iOS
E. Tengo otra preferencia (la indico en Other)
X. Other (especificar)

[Answer]: A — **TypeScript** con React + Expo. Stack confirmado: React Native/Expo + TypeScript.

---

## P3. Entrega de los datos de sensores (afecta las "alertas")

¿Cómo esperas que la app reciba las lecturas de los sensores desde el backend?

A. Tiempo real (el backend empuja datos: WebSocket / push / MQTT)
B. Consulta periódica (la app pregunta cada X segundos/minutos: polling)
C. Ambas / híbrido (histórico por consulta, lo nuevo en tiempo real)
D. No definido aún — lo evaluamos en el diseño
X. Other (especificar)

[Answer]: B — Consulta periódica (polling). Nota: Supabase ofrece realtime si más adelante se quiere; para el MVP el usuario elige polling.

---

## P4. Autenticación / autorización — ¿dónde vive?

Pediste login, registro, autenticación y autorización. ¿Cómo prefieres resolverlo?

A. Lo provee el backend propio (la app solo consume sus endpoints de auth)
B. Un proveedor de identidad gestionado (p.ej. Firebase Auth, Supabase, Auth0, Cognito)
C. La app maneja su propia auth localmente por ahora (mock/simple para el MVP)
D. No definido aún — recomiéndame según el resto del diseño
X. Other (especificar)

[Answer]: B — **Supabase** (proveedor gestionado). Supabase será la base de datos (Postgres) Y el proveedor de auth. La app usa el cliente de Supabase directamente. Esto reconcilia el "backend aparte / contrato TBD" de intent-capture: el backend gestionado es Supabase.

---

## P5. Hosting / distribución del MVP

¿Dónde correrá y cómo la usarás en esta etapa? (elige todas las que apliquen)

A. Web desplegada en un hosting simple (Vercel/Netlify/GitHub Pages o similar)
B. Android como APK instalado manualmente en tu teléfono (sin tienda por ahora)
C. Ejecución solo local / en desarrollo por ahora (sin despliegue formal)
D. Publicación en tiendas (Play Store / App Store) — probablemente futuro, no MVP
X. Other (especificar)

[Answer]: B — Android como APK instalado manualmente. Objetivo de distribución del MVP = **Android (APK sideload)**. REVISADO 2026-07-10: web e iOS eliminados del alcance; Android es la única plataforma.

---

## P6. Restricciones de presupuesto y tiempo

A. Sin presupuesto (usar solo herramientas gratuitas / tiers gratis); tiempo flexible
B. Presupuesto pequeño aceptable (algún servicio de pago barato); tiempo flexible
C. Hay una fecha objetivo (la indico en Other)
D. Sin restricciones relevantes
X. Other (especificar)

[Answer]: B — Presupuesto pequeño (algún servicio de pago barato aceptable); tiempo flexible. (Supabase free/pro y Expo EAS free tier deberían bastar al inicio.)

---

## P7. Datos y privacidad (compliance)

¿Qué tipo de datos manejará y para quién?

A. Solo mis propios datos (cuenta personal); nada sensible ni de terceros
B. Datos de varios usuarios en el futuro, pero mínimos (email + datos de plantas)
C. Podría haber datos de terceros/sensibles más adelante (lo consideramos)
D. No aplica / no me preocupa en esta etapa
X. Other (especificar)

[Answer]: A — Solo mis propios datos (cuenta personal); nada sensible ni de terceros. Compliance mínimo para el MVP.
