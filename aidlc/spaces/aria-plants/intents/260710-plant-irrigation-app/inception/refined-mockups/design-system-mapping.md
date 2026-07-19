# Design System Mapping — Aria Plants (Android MVP)

> Etapa 2.5 · Fase Inception · Alcance **MVP** (profundidad Estándar) · 2026-07-19
> Mapea el diseño refinado (`mockups.md`, `interaction-spec.md`) a una base de
> componentes concreta. Decisión Q3=A: **React Native Paper (Material Design 3)**.
> Cumple `team-practices.md` (stack Expo/RN + TS strict) y `requirements.md`
> (NFR-Usabilidad: dark-first + i18n ES/EN). Objetivo a11y: WCAG 2.1 AA
> (`accessibility-checklist.md`). Solo Android.

## 1. Elección del design system (Q3=A)

**React Native Paper** (v5, Material Design 3). Motivos:
- Tema **oscuro nativo** (MD3 `MD3DarkTheme`) — encaja con dark-first sin trabajo extra.
- Componentes **accesibles** de fábrica (roles, foco, labels) → acelera WCAG AA.
- **Theming por tokens** (color, tipografía, elevación) centralizable en un `ThemeProvider`.
- Buen soporte en **Expo**; TypeScript de primera clase (TS strict de `team-practices.md`).
- Comunidad amplia; reduce componentes propios (equipo de 2, junior incluido).

Alternativas descartadas: componentes propios (más control pero más esfuerzo — no
justificado en MVP de 2 personas); Tamagui/gluestack (potentes pero curva/decisión
mayor sin necesidad para este alcance).

## 2. Design tokens (dark-first)

Tokens semánticos MD3 en tema oscuro. Valores de referencia (hex) — a afinar en
implementación contra el contraste AA; el equipo puede ajustar dentro del rango AA.

### Color (rol MD3 -> uso -> valor ref.)

| Token (rol MD3) | Uso en la app | Valor ref. (dark) |
|-----------------|---------------|-------------------|
| `color.background` (background) | Fondo de pantalla | #101410 |
| `color.surface` (surface) | Cards, appbar, sheets | #171D17 |
| `color.surfaceVariant` | Bordes/inputs, chips neutros | #202821 |
| `color.onBackground` / `onSurface` | Texto principal | #E6EDE6 |
| `color.onSurfaceVariant` | Texto secundario/labels | #B7C2B7 |
| `color.primary` | Acento marca (botones, activos) | #6FD98F (verde sutil) |
| `color.onPrimary` | Texto sobre primary | #06210F |
| `color.error` | Estado BAJA, errores | #FF6B6B |
| `color.onError` | Texto sobre error | #2A0A0A |
| `color.warning` (custom) | Advertencias no críticas | #E7B75A |
| `color.success` (custom) | Estado OK | #6FD98F |
| `color.outline` | Bordes, divisores | #3A463A |

> El **verde es acento sutil**, no tema base (convención `project.md` → UI). El
> fondo es casi-negro verdoso, minimalista. El tema **claro es post-MVP** (no se
> define aquí; solo se deja el theming preparado para añadirlo sin refactor).

### Tipografía (escala MD3)

| Token | Rol MD3 | Uso |
|-------|---------|-----|
| `type.display` | displaySmall | Wordmark "Aria Plants" en auth |
| `type.title` | titleLarge | Título de Appbar |
| `type.titleSmall` | titleMedium | Encabezados de sección (Resumen, Plantas…) |
| `type.body` | bodyMedium | Texto de contenido |
| `type.label` | labelLarge | Labels de campos y botones |
| `type.caption` | bodySmall | Metadatos ("hace 5 min", helpers) |

Fuente: la por defecto del sistema Android (Roboto) vía MD3; sin fuentes custom en MVP.

### Espaciado, radio y elevación

| Token | Valor | Uso |
|-------|-------|-----|
| `space.xs` | 4dp | Gaps internos |
| `space.sm` | 8dp | Separación de labels |
| `space.md` | 16dp | Padding estándar de pantalla/card |
| `space.lg` | 24dp | Separación de secciones |
| `space.xl` | 32dp | Márgenes de auth |
| `radius.sm` | 8dp | Chips, inputs |
| `radius.md` | 12dp | Cards |
| `radius.lg` | 20dp | Sheets |
| `elevation.card` | level1 | Cards sobre surface |
| `elevation.appbar` | level0 | Appbar plana (minimalista) |

Todos los valores viven en un único módulo de tema (p. ej. `theme.ts`) consumido
por `PaperProvider`; ningún color/tamaño hardcodeado en componentes (paralelo a la
regla i18n de "sin strings hardcodeados").

## 3. Mapeo componente de la app -> React Native Paper

| Componente app (`interaction-spec.md`) | Componente RN Paper | Notas |
|----------------------------------------|---------------------|-------|
| AppBar / header | `Appbar.Header`, `Appbar.BackAction`, `Appbar.Action` | Título y acción Editar/+ |
| BottomTabBar (4 tabs) | `BottomNavigation` (o React Navigation + Paper) | Inicio/Plantas/Zonas/Perfil |
| FormField (texto) | `TextInput` (mode="outlined") + `HelperText` | Validación inline, contador si aplica |
| Button primario | `Button` (mode="contained") | color.primary |
| Button secundario/enlace | `Button` (mode="text") | "Crear cuenta", "Entrar" |
| HumidityBadge (OK/BAJA/Sin dato) | `Chip` (custom color) | ícono + texto, no solo color |
| PlantCard / ZoneCard | `Card` + `Card.Content` + `TouchableRipple` | navegación al detalle |
| AlertItem | `List.Item` con `left` ícono | ícono (!) + texto + caption |
| ZoneSelector (con "+ Crear zona") | `Menu`/`List.Accordion` + item CTA | push a §7b si no hay zonas (Q1=A) |
| PlantCheckboxList (zona) | `Checkbox.Item` | asignar/reasignar (HU-D5) |
| LanguageSelector | `SegmentedButtons` | ES/EN (HU-X1) |
| PushToggle | `Switch` + `List.Item` | HU-F3 |
| EmptyState | `Surface` + ícono + texto + `Button` | CTA de creación |
| ErrorBanner | `Banner` | acción Reintentar |
| LoadingSkeleton | placeholders propios (o `ActivityIndicator` en botones) | skeleton atenuado |
| ConfirmDialog | `Dialog` + `Portal` | eliminar planta/zona, cerrar sesión |
| PhotoPicker | expo-image-picker + `Avatar.Image` | foto opcional (HU-B2) |
| PushPriming (§9) | pantalla propia con `Button`s | una vez, Q5=B |

## 4. i18n y theming (integración)

- **i18n (HU-X1, NFR-Usabilidad)**: todo texto visible pasa por i18n (p. ej.
  `i18next`/`expo-localization`); **sin strings hardcodeados** (regla de proyecto).
  Idioma por defecto **español**; selector ES/EN en Login y Perfil.
- **Theming**: `PaperProvider` con `MD3DarkTheme` extendido por los tokens §2. El
  MVP monta solo el tema oscuro; el claro se añade post-MVP cambiando el tema del
  provider (sin tocar componentes).

## 5. Trazabilidad

- Stack y TS strict → `team-practices.md` (Code Style) y `project.md` (Tech Stack).
- Dark-first + i18n + "sin hardcodeo" → `requirements.md` (NFR-Usabilidad/UI) y
  `project.md` (UI Conventions).
- Componentes ↔ pantallas → `mockups.md` (§1–§9) y `wireframes.md` (1.6).
- Accesibilidad de cada componente → `accessibility-checklist.md`.
