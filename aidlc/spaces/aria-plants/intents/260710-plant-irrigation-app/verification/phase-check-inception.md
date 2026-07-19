# Phase Boundary Verification — Inception → Construction

> Etapa 2.8 (Delivery Planning) · 2026-07-19 · Alcance **MVP**
> Verificación de trazabilidad antes de pasar a Construction. Fuentes:
> `requirements.md`, `stories.md`, `personas.md`, `application-design/`,
> `units-generation/`, `refined-mockups/`.

## 1. Requisitos → Historias (cobertura)

| Requisito | Historia(s) | ¿Cubierto? |
|-----------|-------------|-----------|
| RF-A1..A4 (cuentas/sesión/RLS) | HU-A1..A4 | ✔ |
| RF-B1..B4 (CRUD plantas) | HU-B1..B6 | ✔ |
| RF-C1..C2 (sensores) | HU-C1, HU-C2 | ✔ |
| RF-D1..D2 (zonas) | HU-D1..D5 | ✔ |
| RF-E1..E3 (dashboard/humedad) | HU-E1, HU-E2 | ✔ |
| RF-F1..F3 (alertas) | HU-F1..F4 | ✔ |
| NFR-Usabilidad (i18n/dark) | HU-X1 (+ nota dark-first) | ✔ |
| NFR-Seguridad (anon key/RLS) | HU-A4 | ✔ |
| NFR-Frescura (polling) | HU-E2 | ✔ |
| NFR-Plataforma/Calidad | transversales (Bolts + CI) | ✔ |

Sin requisitos funcionales sin historia.

## 2. Historias → Requisitos (sin huérfanas)

Las 24 historias formales de `stories.md` declaran su `Traza` a RF/NFR; la matriz
de trazabilidad de `stories.md` verifica que cada historia mapea a ≥1 requisito.
Sin historias inventadas fuera de `requirements.md` (confirmado por el revisor de
user-stories, veredicto READY).

## 3. Arquitectura/Unidades → Historias (cobertura)

`unit-of-work-story-map.md` asigna las 24 historias a las 8 unidades; toda unidad
tiene ≥1 historia y no hay historias sin unidad. El diseño de `application-design/`
(componentes/servicios) tiene un hogar para cada área funcional (auth, zones,
plants, sensors, readings/dashboard, alerts, profile) + `foundation`.

## 4. Consistencia de decisiones (open questions resueltas)

| Open question | Estado |
|---------------|--------|
| OQ-1 (simulación lecturas) | Resuelto: adaptador `ReadingsSource` + simulador (ADR-004) |
| OQ-2 (almacenamiento foto) | Resuelto: **local** (Q7=C, ADR-007) |
| OQ-3 (borrado cascada) | Resuelto: cascade planta→sensores→lecturas; zona bloqueada con plantas (ADR-006) |
| OQ-refined-1 (agregación multi-sensor) | Resuelto: worst-case en cliente (ADR-005, `computePlantStatus` en foundation) |
| C-4 (contrato sensores) | Aislado tras adaptador; pendiente externamente (no bloquea) |
| OQ-4 (mecanismo push) | Diferido a NFR/Infra Design (ADR-011); push = Should |

## 5. Resultado

**PASA.** Requisitos, historias, mockups, diseño y unidades están alineados y con
trazabilidad completa. Las open questions críticas para construir están resueltas o
aisladas (contrato de sensores) / diferidas sin bloquear (push). El plan de Bolts
(walking-skeleton-first) es un camino topológicamente válido por el DAG.

**Listo para Construction.** Bolt 1 = walking skeleton (gated, solo, senior).
