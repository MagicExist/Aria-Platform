# Scope Document — App de Monitoreo de Riego de Plantas (Android)

> Etapa 1.4 · Fase Ideación · Alcance: MVP · Fecha: 2026-07-10
> Deriva de `intent-statement.md` (intent-capture) y de la viabilidad y
> restricciones en `feasibility-assessment.md` + `constraint-register.md`.

## Resumen de Alcance

App **Android** (React Native/Expo + TypeScript) que actúa como centro de
información de plantas y zonas de riego para un usuario personal. Consume datos
desde **Supabase** (Postgres + Auth). El MVP prueba el flujo completo de cuenta →
registro de plantas → sensores → visualización de **humedad** → alertas, con una
única zona de riego al inicio y capacidad de crecer.

## Dentro del MVP (In-Scope)

1. **Cuentas y sesión** — registro, login, autenticación y autorización con
   Supabase Auth (RLS activo).
2. **Gestión de plantas** — crear, ver, editar y eliminar plantas (CRUD).
3. **Sensores** — registrar sensores y asignarlos a una planta.
4. **Zonas de riego** — agrupar plantas en zonas (mínimo 1 zona en MVP).
5. **Visualización** — dashboard general de plantas/zonas donde se muestran las
   variables; las variables se ven **dentro** del dashboard/vista de planta (no
   en una pantalla dedicada).
6. **Variable soportada en MVP: HUMEDAD únicamente.**
7. **Alertas** — aviso cuando la humedad sale de rango, entregadas **en-app y por
   notificación push**. Umbrales **fijos** en el MVP.

## Fuera del MVP (Out-of-Scope)

- **Indicador derivado de "salud"** de la planta (diferido, ya señalado en
  `intent-statement.md`).
- **Variable de LUZ** (y cualquier otra variable distinta de humedad).
- **Multiusuario / compartir** plantas o zonas con otras personas.
- **Umbrales personalizados** por planta/tipo (MVP usa umbrales fijos).
- **Web e iOS** (decidido en el gate de feasibility; la app es solo Android — ver
  `constraint-register.md` TC-1/TC-3).
- **Publicación en tiendas** (distribución MVP = APK, ver `constraint-register.md` TC-8).

### "Could" — no priorizado (ni dentro ni excluido)
- **Histórico / gráficas de tendencias** en el tiempo — se considerará solo si
  sobra tiempo; no compromete el MVP.

## Priorización (MoSCoW)

| Prioridad | Capacidad |
|-----------|-----------|
| Must | Cuentas y sesión (auth) |
| Must | CRUD de plantas |
| Must | Registro y asignación de sensores |
| Must | Zonas de riego |
| Must | Dashboard general con lectura de humedad |
| Must | Alertas de humedad fuera de rango (in-app + push) |
| Could | Histórico / tendencias |
| Won't (MVP) | Indicador de "salud", variable de luz, multiusuario, umbrales personalizados, web, iOS |

## Walking Skeleton (primera rebanada de extremo a extremo)

**Login → registrar 1 planta → asignarle 1 sensor → ver su humedad en el
dashboard.** Es la rebanada de menor riesgo que ejercita todos los puntos de
integración: Auth de Supabase, escritura/lectura de datos en Postgres, y el
render de una lectura en la UI Android. Alimenta el Bolt 1 en delivery-planning.

## Value Stream (capacidad → resultado para el usuario)

- Cuenta → acceso seguro y datos propios aislados (RLS).
- Plantas + sensores + zonas → estructura de información organizada.
- Dashboard con humedad → visibilidad del estado real de las plantas.
- Alertas → reacción temprana para optimizar el riego (resultado central del intent).

## Restricciones y supuestos heredados

- Del `constraint-register.md`: solo Android (TC-1), stack Expo/RN+TS (TC-2),
  este repo es solo la app + Supabase (TC-4), lecturas por polling (TC-5),
  contrato de sensores por definir (TC-6), auth por Supabase (TC-7).
- Del `feasibility-assessment.md`: veredicto altamente viable; dependencia
  crítica = contrato de datos de sensores (hasta entonces, datos simulados).
- **Diferido** (open question a resolver en Application Design): si la app
  incluye ingreso/simulación de lecturas — front y backend se desarrollan en
  paralelo, así que el origen de datos se aísla tras una capa/adaptador.
