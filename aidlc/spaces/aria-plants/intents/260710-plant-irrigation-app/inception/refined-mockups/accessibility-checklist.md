# Accessibility Checklist — Aria Plants (Android MVP)

> Etapa 2.5 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Objetivo (Q4=A): **WCAG 2.1 nivel AA**, aplicado a React Native / Android con
> **TalkBack** como lector de referencia. Deriva de `mockups.md`,
> `interaction-spec.md`, `design-system-mapping.md`, `stories.md`,
> `requirements.md`. Cada ítem es verificable (pass/fail) para Build & Test.

## 1. Criterios transversales (toda pantalla)

| # | Criterio (WCAG) | Cómo se cumple | Verificación |
|---|-----------------|----------------|--------------|
| A1 | Contraste de texto ≥ 4.5:1 (1.4.3) | Tokens dark de `design-system-mapping.md` validados | Medir texto/fondo con checker; falla si <4.5:1 |
| A2 | Contraste de UI/íconos ≥ 3:1 (1.4.11) | Bordes `color.outline`, chips, iconos | Medir; falla si <3:1 |
| A3 | No depender solo del color (1.4.1) | Estado siempre con **ícono + texto** (OK/BAJA/Sin dato; alertas con !) | Revisión visual + código de HumidityBadge/AlertItem |
| A4 | Todo control tiene nombre accesible (4.1.2) | `accessibilityLabel`/label i18n en cada control | TalkBack lee un nombre no vacío en cada control |
| A5 | Touch target ≥ 48dp (2.5.5 AAA como práctica MVP) | Botones/tarjetas/toggles ≥48dp | Inspección de estilos |
| A6 | Orden de foco lógico (2.4.3) | Orden DOM/árbol coherente con lectura visual | Navegar con TalkBack en orden esperado |
| A7 | Foco visible / gestión de foco (2.4.7) | Foco inicial correcto por pantalla; trap en diálogos | TalkBack + teclado externo |
| A8 | Texto escalable sin romper layout (1.4.4) | Respeta el font scaling del sistema Android | Probar a 130%/200% sin recortes |
| A9 | Idioma del contenido correcto (3.1.x) | i18n ES/EN; el contenido cambia 100% con el selector | Cambiar idioma; verificar que TalkBack usa el idioma correcto |
| A10 | Mensajes de estado anunciados (4.1.3) | ErrorBanner/alertas como role status/alert; skeleton como "cargando" | TalkBack anuncia error/carga sin mover foco |
| A11 | Objetivo de error identificado y descrito (3.3.1/3.3.3) | Validación inline con texto claro por campo | Provocar error; leer mensaje |

## 2. Checklist por pantalla

### Login / Registro
- [ ] `h1` = "Aria Plants" / "Crear cuenta"; formulario en landmark principal.
- [ ] Foco inicial en Correo (Login) / Nombre (Registro).
- [ ] Campos con label asociado (no placeholder-only); toggle "mostrar contraseña" etiquetado.
- [ ] Errores (credenciales, correo duplicado, política, contraseñas no coinciden) inline y anunciados; foco al primer error.
- [ ] SegmentedButtons de idioma operables por TalkBack; estado seleccionado anunciado.
- Traza: HU-A1, HU-A2, HU-X1.

### Dashboard
- [ ] `h1` marca + `h2` por sección (Resumen, Alertas recientes, Plantas).
- [ ] AlertItem con ícono (!) + texto (no solo color); antigüedad legible.
- [ ] HumidityBadge anuncia valor + estado; "Sin dato" cuando el sensor nunca reportó (Q6=A).
- [ ] Estado Loading anunciado como "cargando"; Error como alerta con Reintentar accesible.
- [ ] Alerta resuelta (HU-F4): al recuperarse la humedad, TalkBack anuncia "alerta resuelta" y la fila sale de "Alertas recientes" sin mover el foco bruscamente.
- [ ] Pull-to-refresh accesible o alternativa por botón.
- Traza: HU-E1, HU-E2, HU-F2, HU-F4, HU-A4.

### Plantas lista / Zonas lista
- [ ] `h1` de la pantalla; cada tarjeta es un control con nombre accesible completo ("Menta, zona Balcón, humedad 18%, estado bajo").
- [ ] Botón "+" etiquetado ("Agregar planta"/"Crear zona"); target ≥48dp.
- [ ] Empty state con texto guía legible y CTA como botón etiquetado.
- Traza: HU-B3, HU-D2.

### Planta detalle
- [ ] `h1` = nombre de planta; `h2` Humedad / Sensores.
- [ ] Foto opcional con `accessibilityLabel` ("Foto de Menta") o marcador anunciado si no hay.
- [ ] Lista de sensores: cada uno anuncia última lectura o "Sin dato".
- [ ] "Eliminar planta" abre diálogo de confirmación con foco en Cancelar.
- Traza: HU-B4, HU-C2, HU-B2, HU-B6.

### Crear/editar planta · Asignar sensor · Crear/editar zona
- [ ] `h1` del formulario; foco inicial en el primer campo (Nombre).
- [ ] Cada control con label; `ZoneSelector` anuncia "Zona de riego, requerido" y la acción "Crear zona".
- [ ] Guardar deshabilitado hasta cumplir requeridos (Nombre + Zona); estado disabled anunciado.
- [ ] PhotoPicker: botón "Añadir/Cambiar foto" etiquetado; imagen con `accessibilityLabel`; si el permiso se deniega, se anuncia y la planta se guarda sin foto (no bloquea).
- [ ] PlantCheckboxList: cada checkbox etiquetado con el nombre de la planta.
- [ ] Confirmación de eliminar zona con foco en Cancelar; caso "zona con plantas" explica el bloqueo (HU-D4).
- Traza: HU-B1, HU-B2, HU-C1, HU-D1, HU-D3, HU-D4, HU-D5, zona-primero.

### Perfil / Permiso push
- [ ] `h1` Perfil; `h2` Ajustes; cada ajuste con label.
- [ ] Idioma (SegmentedButtons) y PushToggle (Switch) operables y anunciados; estado "Permiso denegado" legible.
- [ ] "Cerrar sesión" con confirmación (foco en Cancelar).
- [ ] Pantalla de priming de push (§9): botones "Activar notificaciones" y "Ahora no" etiquetados; texto explicativo legible.
- Traza: HU-A3, HU-X1, HU-F3.

## 3. Fuera de alcance del MVP (accesibilidad)

- Tema **claro** (alternancia claro/oscuro) — post-MVP (dark-first). El contraste
  se garantiza en el tema oscuro; al añadir el claro se re-validará AA.
- Soporte iOS VoiceOver — no aplica (solo Android, `project.md`).
- Auditoría AAA completa — el objetivo es **AA**; ítems AAA (p. ej. contraste 7:1)
  se adoptan solo donde ya se cumplen sin coste (targets ≥48dp).

## 4. Verificación en construcción

- Los ítems marcados se verifican en **Build and Test (3.6)** como parte de la
  cobertura de UI; los criterios de contraste/labels son revisables por
  lint/inspección y por pruebas de componente (RN Testing Library expone roles y
  labels accesibles). CI debe mantenerse en verde (NFR-Calidad).
