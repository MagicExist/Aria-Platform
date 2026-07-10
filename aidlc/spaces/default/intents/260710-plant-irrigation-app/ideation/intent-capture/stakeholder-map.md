# Stakeholder Map — App de Monitoreo de Riego de Plantas

> Etapa 1.1 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Proyecto personal: el conjunto de stakeholders es reducido. Se documentan
> también los roles "externos" (backend, dispositivos) que la app depende pero
> que viven fuera de este repositorio.

## Key Stakeholders and Interests

| Stakeholder | Rol | Interés principal |
|-------------|-----|-------------------|
| Autor / Propietario del proyecto | Usuario final, desarrollador y dueño del producto | Aprender construyendo; tener un centro único para monitorear sus plantas; que la app funcione en web y Android (iOS compilable) |
| Usuario "aficionado en casa" (rol) | Quien usa la app día a día (hoy = el autor) | Ver el estado de cada planta, organizar por zonas, recibir alertas, gestionar plantas y sensores con una cuenta propia |
| Equipo/proyecto del Backend de sensores | Dependencia externa (fuera de este repo) | Definir y proveer el contrato de la API que la app consumirá; entregar los datos de sensores |
| Sensores / dispositivos IoT (futuros) | Fuente de datos | Emitir lecturas de humedad y luz asociadas a plantas/zonas |
| Futuros usuarios (post-MVP) | Stakeholder potencial | Que el diseño permita escalar a más plantas, zonas y usuarios |

## Decision-Makers vs. Influencers

- **Decision-maker (único):** el Autor/Propietario. Toma todas las decisiones de
  producto, alcance y aprobación de cada etapa (aprueba los gates de AI-DLC).
- **Influencers / restricciones externas:**
  - El **contrato del backend de sensores** (aún por definir) condicionará cómo
    la app recibe y modela los datos — influye fuertemente en el diseño, pero no
    es una persona que decida; es una dependencia técnica a acordar.
  - La **ausencia de hardware Mac** influye en la estrategia de iOS (build sí,
    verificación local no).

## Communication Requirements

- **Cadencia de decisión:** síncrona e inmediata — el autor es a la vez usuario
  y decisor, por lo que las aprobaciones ocurren en cada gate de AI-DLC dentro
  de esta misma sesión de trabajo.
- **Idioma de trabajo:** español (preguntas, resúmenes y decisiones).
- **Coordinación externa pendiente:** acordar el **contrato de datos** con el
  proyecto del backend antes de que Inception/Construction endurezca el modelo
  de datos de sensores. Hasta entonces, la app trabaja con un contrato asumido o
  datos simulados.
- **Sin requisitos de reporte a terceros** en el MVP (no hay clientes externos,
  cumplimiento regulatorio ni patrocinadores en esta etapa).
