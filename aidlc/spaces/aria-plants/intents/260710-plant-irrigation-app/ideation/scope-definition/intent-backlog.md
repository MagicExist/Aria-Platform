# Intent Backlog — Proto-Units priorizados

> Etapa 1.4 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Backlog de proto-Units (capacidades independientes que luego se refinan en
> Units Generation). Priorizado con MoSCoW; el orden de construcción real lo
> fija delivery-planning. Deriva del `scope-document.md` y del `intent-statement.md`.

## Proto-Units (MVP)

| ID | Proto-Unit | Prioridad | Depende de | Valor / qué prueba |
|----|-----------|-----------|------------|--------------------|
| PU-1 | **Cuentas y sesión** (registro, login, logout, sesión persistente con Supabase Auth) | Must | — | Acceso seguro; base para RLS y datos por usuario |
| PU-2 | **Gestión de plantas** (CRUD de plantas) | Must | PU-1 | Estructura central de información |
| PU-3 | **Sensores** (registrar sensor, asignar a planta) | Must | PU-2 | Vincula el mundo físico con la planta |
| PU-4 | **Zonas de riego** (crear zona, asignar plantas) | Must | PU-2 | Organización por zona |
| PU-5 | **Dashboard + lectura de humedad** (vista general; humedad por planta) | Must | PU-2, PU-3 | Visibilidad del estado (variable = humedad) |
| PU-6 | **Alertas de humedad** (evaluar umbral fijo; mostrar in-app + push) | Must | PU-5 | Reacción temprana (resultado central) |

## Walking Skeleton (primer corte e2e)

Atraviesa PU-1 → PU-2 → PU-3 → PU-5 (mínimo): **login, 1 planta, 1 sensor, ver su
humedad**. Prueba Auth + persistencia + render de lectura en Android.

## Diferido / no priorizado

| ID | Proto-Unit | Estado |
|----|-----------|--------|
| PU-7 | Histórico / gráficas de tendencias | Could — solo si sobra tiempo |
| — | Indicador de "salud" derivado | Won't (post-MVP) |
| — | Variable de luz (y otras variables) | Won't (post-MVP) |
| — | Multiusuario / compartir | Won't (post-MVP) |
| — | Umbrales personalizados por planta/tipo | Won't (post-MVP; MVP usa fijos) |
| — | Soporte web / iOS | Won't (fuera de alcance del proyecto) |

## Notas de dependencia y secuencia

- PU-1 (auth) es la raíz: casi todo depende de tener usuario y RLS.
- PU-2 (plantas) es el núcleo del modelo; PU-3, PU-4 y PU-5 cuelgan de él.
- PU-6 (alertas) depende de tener lecturas visibles (PU-5).
- La forma exacta del origen de datos (real vs simulado/ingreso manual) está
  **diferida** a Application Design; no bloquea la priorización.
- El orden económico de Bolts (qué se construye primero más allá del skeleton) lo
  decide delivery-planning en Inception.
