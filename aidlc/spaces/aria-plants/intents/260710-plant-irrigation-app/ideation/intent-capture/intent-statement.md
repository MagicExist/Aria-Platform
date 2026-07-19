# Intent Statement — App de Monitoreo de Riego de Plantas

> Etapa 1.1 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Documento a nivel de problema/oportunidad. Sin decisiones técnicas (esas
> se definen en Feasibility y Application Design).
>
> **Revisión 2026-07-10 (en gate de feasibility):** el alcance de plataformas se
> redujo a **solo Android**. Se eliminó el soporte web y iOS (iOS no es
> verificable sin Mac; web queda fuera del MVP). El objetivo multiplataforma
> original se reemplaza por "app Android con React Native/Expo".

## Problem Statement

Una persona que cuida un conjunto de plantas (inicialmente ~11 plantas en una
zona de riego) no cuenta hoy con una forma centralizada de ver las condiciones
de sus plantas ni de reaccionar a tiempo cuando algo se sale de lo normal.
La información de las variables ambientales (humedad, luz) que producirán los
sensores está —o estará— dispersa y sin un lugar único donde consultarla,
organizarla por zonas y recibir avisos.

El problema a resolver es la **falta de un centro de información** que:
1. reúna en un solo lugar los datos que envían los sensores de cada planta,
2. permita organizar las plantas y sus sensores por zonas de riego,
3. y avise tempranamente cuando una variable sale de su rango esperado,
para poder optimizar el riego y reducir la pérdida de plantas.

Es además un proyecto de **aprendizaje/exploración** personal: construir una
**app Android** (React Native/Expo) conectada a datos de sensores (IoT) es un
objetivo explícito en sí mismo, no solo un medio.

## Target Customer

- **Usuario principal (MVP):** un aficionado que cuida plantas en casa
  (jardín doméstico). En esta etapa es un único usuario, el propio autor.
- **Dolor que experimenta:** no puede ver de un vistazo el estado de cada
  planta, ni tiene alertas; el seguimiento hoy sería manual o inexistente.
- **Beneficio:** un tablero único, accesible desde su teléfono Android, que
  muestra las variables de cada planta, las agrupa por zona de riego y notifica
  anomalías.
- **Evolución esperada:** el diseño debe permitir crecer a más plantas, más
  zonas y potencialmente más usuarios en el futuro (no es un requisito del MVP,
  pero sí una dirección declarada).

## Success Metrics

Métricas medibles para considerar exitoso el MVP (proyecto personal — metas
conservadoras y verificables):

1. **Centralización:** el 100% de las plantas registradas (meta inicial ~11)
   muestran sus variables de sensores (humedad, luz) en un único tablero.
2. **Organización:** las plantas pueden agruparse en al menos 1 zona de riego,
   y cada planta puede tener sensores asignados y consultables.
3. **Alertas:** ante una lectura fuera de rango, la app genera una alerta
   visible para el usuario (meta: la alerta aparece en la sesión del usuario;
   el umbral de tiempo/entrega se define en etapas técnicas posteriores).
4. **Acceso en Android:** un usuario puede crear cuenta, iniciar sesión y usar
   la app en un dispositivo Android (instalada como APK).
5. **Aprendizaje (meta cualitativa):** una app Android funcional que consume
   datos de un backend de sensores, ejecutable/instalable de extremo a extremo.

> Supuesto: los umbrales de "fuera de rango" y las variables exactas por tipo de
> planta se detallarán en Inception; aquí solo se fija que la capacidad de
> alerta existe.

## Initiative Trigger

**¿Por qué ahora?** El detonante es de **aprendizaje y exploración**: el autor
quiere construir una app Android conectada a sensores IoT. Existe (o llegará)
hardware de sensores para plantas y falta el software que reciba, centralice y
visualice esos datos. No hay presión de mercado ni regulatoria; la
motivación es personal y de desarrollo de habilidades, con la puerta abierta a
escalar más adelante.

## Initial Scope Signal

- **Alcance seleccionado:** **MVP** (profundidad Estándar). Confirmado por el
  usuario pese al perfil personal, para cubrir Ideación → Inception →
  Construction con autenticación y dashboards.

**Dentro del MVP:**
- Registro/gestión de plantas.
- Asignación de sensores a cada planta.
- Agrupación de plantas por zonas de riego.
- Recepción y visualización de variables de sensores (humedad, luz) por planta.
- Dashboards para visualizar la información.
- Alertas ante variables fuera de rango.
- Login: creación de cuentas, autenticación y autorización.
- Plataforma: **solo Android** (React Native/Expo), instalada como APK.

**Fuera del MVP (post-MVP / futuro):**
- Indicador derivado de **"salud" de la planta** a partir de las variables
  (explícitamente diferido por el usuario).
- Escalado a múltiples usuarios/organizaciones y cientos de plantas (dirección,
  no requisito del MVP).

**Constraints / contexto (no son decisiones de implementación):**
- Este repositorio contiene **únicamente la app**; el **backend se construye por
  separado**. El **contrato de datos de sensores está por definir**; hasta que
  exista, la app asume un contrato/simulación.
- Green-field: no hay solución previa que reemplazar.
