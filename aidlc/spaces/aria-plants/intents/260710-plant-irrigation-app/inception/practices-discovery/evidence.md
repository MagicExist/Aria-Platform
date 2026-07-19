# Evidence — Practices Discovery

> Etapa 2.2 · Fase Inception · Fecha: 2026-07-10
> Rastro de frescura para re-ejecuciones. Proyecto **greenfield**.

## Modo de descubrimiento

**Greenfield** — no hay código, git history de features, ni configuración previa
que escanear. Se OMITIÓ el Step 2 (escaneo multi-agente de evidencia). Todas las
prácticas provienen de la **entrevista** (Step 3), usando los defaults de
`org.md` como texto sugerido.

## Por área de práctica

| Área | Fuente | Resultado |
|------|--------|-----------|
| Way of Working | Entrevista P1; default org | Afirmado default: trunk-based + squash-merge |
| Walking Skeleton | Entrevista P2 (con explicación al usuario); default greenfield | Afirmado: skeleton-first, Bolt 1 gated |
| Testing Posture | Entrevista P3; default org MVP | Afirmado default: ~80% cobertura + CI antes de merge |
| Deployment | Entrevista P4 | **Override** del default org: APK manual (EAS/local), sin staging/prod |
| Code Style | Entrevista P5 | Especificado: ESLint + Prettier + TypeScript strict (Expo/RN) |

## Notas

- El único **override** del default de organización es Deployment: este proyecto
  personal Android no usa entornos de servidor; se distribuye por APK.
- El resto se alinea con los defaults de `org.md` (trunk+squash, skeleton-first
  greenfield, ~80% + CI) o los especializa (Code Style concreto para Expo/RN).
- Sin código aún: en una futura re-ejecución (brownfield) el escaneo de evidencia
  podrá confirmar o ajustar estas prácticas contra el repo real.
