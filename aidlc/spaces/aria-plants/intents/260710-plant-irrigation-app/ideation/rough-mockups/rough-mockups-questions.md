# Rough Mockups — Preguntas de Clarificación

> Etapa 1.6 · Fase Ideación · Alcance: MVP (profundidad Estándar)
> App **solo Android** (Expo/RN). Wireframes de baja fidelidad + flujo de usuario.
> Contexto: pantallas del MVP giran en torno a cuentas, plantas, sensores, zonas,
> dashboard con humedad y alertas.
> Responde escribiendo la(s) letra(s) en `[Answer]:`.

---

## P1. ¿En qué idioma estará la interfaz del MVP?

A. Español
B. Inglés
C. Ambos (con selector de idioma)
X. Other (especificar)

[Answer]: C — Ambos (español e inglés) con selector de idioma. Implica i18n desde el inicio.

---

## P2. ¿Qué patrón de navegación principal prefieres para la app Android?

A. Barra de pestañas inferior (bottom tabs) — p.ej. Dashboard | Plantas | Zonas | Alertas | Perfil
B. Menú lateral (drawer) que se desliza desde el borde
C. Pila simple con una pantalla de inicio y navegación por botones
D. Recomiéndame lo más estándar para Android
X. Other (especificar)

[Answer]: A — Barra de pestañas inferior (bottom tabs).

---

## P3. Confirma las PANTALLAS clave del MVP (elige todas las que apliquen)

A. Autenticación (login + registro)
B. Dashboard / vista general (plantas y su humedad, alertas recientes)
C. Lista de plantas + detalle de planta (con su humedad y sensores)
D. Crear/editar planta y asignar sensor
E. Zonas de riego (lista + gestión)
F. Pantalla/lista de alertas
G. Perfil / ajustes (cerrar sesión, umbral, idioma)
X. Other (especificar)

[Answer]: A, B, C, D, E, G — Pantallas del MVP: Autenticación, Dashboard, Plantas (lista+detalle), Crear/editar planta, Zonas de riego, Perfil/ajustes. NOTA: (F) NO habrá pantalla/tab dedicada de Alertas; las alertas se muestran DENTRO del Dashboard (+ push). Tabs inferiores = Dashboard · Plantas · Zonas · Perfil.

---

## P4. ¿Cuál es la pantalla inicial tras iniciar sesión?

A. Dashboard / vista general
B. Lista de plantas
C. Lista de zonas de riego
D. Recomiéndame
X. Other (especificar)

[Answer]: A — Dashboard / vista general.

---

## P5. Estilo visual y marca (elige todas las que apliquen)

A. Tema verde/naturaleza (acorde a plantas)
B. Soporte de modo claro y oscuro
C. Usar el nombre "Aria Plants" como marca/título
D. Sin preferencia — algo limpio y minimalista por defecto
X. Other (especificar)

[Answer]: C, D + Other — Marca "Aria Plants"; estilo limpio y minimalista; **modo oscuro como tema principal** (dark-first; claro opcional/después). No se pidió tema verde explícito (se puede usar verde solo como acento sutil).

---

## P6. Requisitos de accesibilidad para el MVP

A. Baseline razonable (contraste adecuado, textos legibles, toques grandes) — sin exigencia formal
B. Cumplir WCAG 2.1 AA de forma explícita
C. No es prioridad en el MVP
X. Other (especificar)

[Answer]: A — Baseline razonable (contraste, textos legibles, toques grandes); sin exigencia formal de WCAG.
