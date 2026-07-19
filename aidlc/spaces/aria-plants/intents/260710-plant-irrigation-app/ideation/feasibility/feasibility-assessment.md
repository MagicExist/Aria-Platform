# Feasibility Assessment — App de Monitoreo de Riego de Plantas

> Etapa 1.3 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Basado en el `intent-statement.md` de intent-capture y en las respuestas de
> `feasibility-questions.md`. Síntesis de arquitecto + plataforma + cumplimiento.
>
> **Revisión 2026-07-10 (gate feasibility):** alcance reducido a **solo Android**.
> Se elimina soporte web e iOS. Sin cambios en el stack (Expo/React Native +
> TypeScript + Supabase), solo se acota el target a Android.

## Resumen de Viabilidad

**Veredicto: ALTAMENTE VIABLE.** La combinación **Expo (React Native + TypeScript)
para una app Android** y **Supabase como backend gestionado (Postgres +
Auth + API)** es un stack maduro y ampliamente documentado que cubre de forma
directa los objetivos del `intent-statement.md`: centralizar datos de sensores,
organizar plantas por zonas de riego, mostrar dashboards, generar alertas y
ofrecer login/registro/autenticación/autorización. Acotar a una sola plataforma
(Android) reduce la superficie y elimina la incertidumbre de iOS/web.

El alcance del MVP es pequeño (1 usuario, ~11 plantas, 1 zona), lo que reduce el
riesgo de escala. Las capacidades diferidas (indicador de "salud", multiusuario a
gran escala) no comprometen el diseño inicial si se respetan buenas fronteras.

## Viabilidad Técnica

| Capacidad del intent | Cómo se resuelve | Viabilidad |
|----------------------|------------------|-----------|
| App Android | Expo / React Native (TypeScript), target Android | Alta |
| Registro de plantas / sensores / zonas | Tablas en Postgres (Supabase) + CRUD vía cliente Supabase | Alta |
| Recibir y mostrar variables (humedad, luz) | La app lee de Supabase mediante **polling** periódico | Alta |
| Dashboards de visualización | Componentes de UI + librerías de gráficos de React | Alta |
| Alertas por variables fuera de rango | Evaluación de umbrales en la app (y/o reglas en Supabase) | Media-Alta |
| Login / auth / autorización | **Supabase Auth** + Row Level Security (RLS) para autorización | Alta |

- **Frontera de este repo:** contiene únicamente la app. Supabase es un servicio
  gestionado (no código de este repo). La **ingesta de lecturas de sensores hacia
  Supabase** (sensores → base de datos) vive fuera de este repo; la app solo
  **lee** los datos.
- **Autorización:** con un solo usuario es trivial, pero Supabase RLS permite
  dejar preparada la autorización por usuario para el escalado futuro sin rediseño.
- **Alertas con polling:** viable; la latencia de una alerta será, como máximo, el
  intervalo de polling. Aceptable para humedad/luz de plantas (cambios lentos).

## Análisis de Riesgos Técnicos

1. **Contrato de datos de sensores indefinido** (riesgo principal). No está
   definido el esquema exacto de las lecturas ni cómo llegan a Supabase. Mitigación:
   diseñar contra un contrato asumido + datos simulados (seed/mock en Supabase)
   hasta que exista la fuente real. Se detalla en Application Design.
2. **Latencia de alertas con polling.** Mitigación: intervalo de polling
   configurable; migrar a realtime de Supabase si se requiere menor latencia.
3. **Bloqueo de proveedor (Supabase).** Riesgo bajo/aceptable para un proyecto
   personal; Postgres es estándar y portable si hiciera falta migrar.

> El riesgo previo de "iOS no verificable localmente" queda **eliminado** al
> reducir el alcance a solo Android.

## Perspectiva de Plataforma / Infraestructura

> Nota importante: el framework AI-DLC asume por defecto un stack **AWS**
> (Bedrock, CDK, Well-Architected). **Este proyecto NO usa AWS.** La perspectiva
> de plataforma se reinterpreta para el stack elegido.

- **Backend/datos:** Supabase gestionado (sin infraestructura propia que operar).
- **Distribución de la app:** **solo Android**, vía **APK** (EAS Build / build
  local), instalado manualmente; sin publicación en tiendas para el MVP.
- **Sin web ni iOS:** eliminados del alcance. El código Expo/React Native se
  configura con target Android únicamente.
- **Costo:** los tiers gratuitos/bajos de Supabase y Expo EAS cubren el MVP;
  presupuesto pequeño confirmado por el usuario.
- **Sin IaC (CDK/CloudFormation)** para el MVP: no hay recursos de nube propios;
  la configuración de Supabase se gestiona desde su panel/migraciones.

## Perspectiva de Cumplimiento

- **Clasificación de datos:** el MVP maneja solo los datos personales del propio
  autor (email de la cuenta + datos de plantas/sensores). No hay PII de terceros,
  ni PHI, ni datos de pago. Sensibilidad: baja.
- **Marcos regulatorios aplicables:** ninguno obligatorio para el MVP (no PCI, no
  HIPAA, no datos de salud humana). Si en el futuro hay multiusuario, aplicaría
  higiene básica de privacidad (consentimiento, borrado de cuenta) — no bloqueante ahora.
- **Controles mínimos recomendados:** usar Supabase Auth (no credenciales
  propias), habilitar RLS desde el inicio, no exponer llaves de servicio en el
  cliente (solo la anon key), y no hardcodear secretos (usar variables de entorno).
- **Veredicto de cumplimiento:** sin bloqueadores. Compliance mínimo.

## Conclusión

No se identifican bloqueadores de viabilidad. Se recomienda **proceder** a Scope
Definition. La única dependencia externa relevante a rastrear es el **contrato de
datos de sensores**; hasta que exista, se trabaja con contrato asumido + datos
simulados. Ver `constraint-register.md` y `raid-log.md`.
