# Personas — Aria Plants

> Etapa 2.4 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Deriva de `requirements.md` (2.3), `intent-statement.md` (1.1) y las respuestas
> de `user-stories-questions.md` (Q1=A: una sola persona activa).
> Las personas son los **usuarios de la app** (cuidadores de plantas), no el
> equipo de desarrollo (ese desnivel senior/junior es input de Delivery Planning).

## Persona principal (activa en el MVP)

### El Cuidador — "Diego", hobbyista doméstico de plantas

| Atributo | Detalle |
|----------|---------|
| **Rol** | Dueño y cuidador de sus propias plantas en casa; único usuario real al inicio (S-1). |
| **Nivel técnico** | Cómodo con apps móviles Android; no es experto en jardinería ni en tecnología de sensores. |
| **Contexto de uso** | Teléfono Android, en casa, sesiones cortas y frecuentes (revisar de pasada) más alguna sesión de gestión (dar de alta plantas/zonas). Tema oscuro por preferencia y por uso nocturno. |
| **Idioma** | Español como idioma de trabajo; puede alternar a inglés (i18n ES/EN). |

**Objetivos (jobs-to-be-done)**
- Tener **un solo lugar** donde ver el estado de humedad de cada planta sin ir revisando físicamente maceta por maceta.
- **Organizar** sus plantas por zonas de riego para razonar sobre el riego de forma agrupada.
- Que la app le **avise a tiempo** cuando una planta se está secando (humedad por debajo del mínimo), para regar antes de perder la planta.
- Aprender/observar la salud de sus plantas a través de datos reales de sensores.

**Pain points (situación actual, sin la app)**
- No sabe cuándo regar: riega "por intuición" y a veces tarde (planta ya marchita) o de más.
- La información de humedad, si existe, está dispersa o no es visible de un vistazo.
- No tiene forma de agrupar/estructurar plantas ni de recibir un aviso proactivo.

**Motivaciones y actitudes**
- Valora la **simplicidad** y la señal clara ("¿necesita agua o no?") por encima de gráficos complejos.
- Quiere **fiabilidad del aviso** más que precisión analítica: prefiere una alerta oportuna a un dashboard exhaustivo.
- Es tolerante a la configuración inicial (dar de alta zonas/plantas/sensores) si luego el día a día es sin fricción.

**Recorrido con la app (resumen)**
1. Crea su cuenta e inicia sesión.
2. Crea al menos **una zona de riego** (obligatorio antes de registrar plantas — regla "zona-primero", Q5).
3. Registra sus plantas dentro de una zona y les **asigna sensores**.
4. Consulta el **dashboard** de humedad en su día a día.
5. Recibe **alertas** (lista in-app; push si está disponible) cuando una planta baja del umbral, y ve cómo se resuelven solas al recuperarse la humedad.

**Cómo Aria Plants le sirve**
- Centraliza la lectura de humedad por planta y por zona en una vista.
- Fuerza una organización mínima (zonas) que da estructura al dashboard.
- Convierte el riego reactivo/intuitivo en riego informado por una alerta clara.

---

## Priorización de personas

| # | Persona | Estado en MVP | Prioridad |
|---|---------|---------------|-----------|
| 1 | **El Cuidador** (hobbyista doméstico) | Activa | Alta — única persona modelada |

Se modela **una sola persona** (decisión Q1=A). El MVP se diseña para un único
usuario real (S-1); el modelo de datos y la autenticación con aislamiento por
usuario (RF-A4 / RLS) permiten crecer a más usuarios sin ser requisito del MVP.

## Consideraciones de futuro (fuera del MVP, no modeladas como persona)

Estas NO son personas del MVP; se anotan solo para no cerrar puertas de diseño:
- **Cuidador con muchas plantas / mini-huerto urbano** — mayor volumen de plantas
  y zonas; podría motivar filtros, búsqueda o vistas por zona más ricas.
- **Escenario multiusuario / compartir** — explícitamente **fuera de alcance**
  (`requirements.md` → Out of Scope). El aislamiento por RLS es la base técnica si
  algún día se aborda.
