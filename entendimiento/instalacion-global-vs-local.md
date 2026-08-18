# Global vs Local — Cuándo usar cada uno

## Resumen

| Aspecto | Global | Local |
|---------|--------|-------|
| Sub-agentes | Compartidos en todos | Específicos del proyecto |
| Skills | Compartidos en todos | Específicos del proyecto |
| OpenSpec | ⚠️ Uno solo para todos | ✅ Uno por proyecto |
| Persona | Compartida | Puede variar |
| AGENTS.md proyecto | ❌ No aplica | ✅ Específico del proyecto |

---

## Global (`~/.config/opencode/`)

### Ventajas

| Beneficio | Descripción |
|-----------|-------------|
| Fácil mantenimiento | Una sola configuración |
| Consistencia | Misma persona, mismos skills en todos los proyectos |
| Menos repetitivo | No reinstalar en cada proyecto |

### Desventajas

| Problema | Ejemplo |
|----------|---------|
| OpenSpec único | Proyecto Node.js y proyecto Go → config.yaml diferente |
| Skills irrelevantes | Skill de React en proyecto Go |
| Sub-agentes que no aplican | `mi-analisis` no tiene sentido en proyecto Python |
| Conflictos de config | Stacks, arquitectura, convenciones diferentes |

### Cuándo usar GLOBAL

| Escenario | Por qué funciona |
|-----------|-----------------|
| Stacks similares | Todos tus proyectos son Node.js + TypeScript |
| Mismas convenciones | Conventional commits, mismo linter |
| misma persona | Siempre Gentleman o Neutral |
| Skills compartidas | Testing patterns, docs |

---

## Local (`{project-root}/.config/opencode/`)

### Ventajas

| Beneficio | Descripción |
|-----------|-------------|
| Config específica | Stack, arquitectura, convenciones del proyecto |
| Skills pertinentes | Solo las que aplican al stack |
| Sub-agentes relevantes | Solo los que el proyecto necesita |
| OpenSpec por proyecto | Cada proyecto tiene su propia estructura |

### Desventajas

| Problema | Descripción |
|----------|-------------|
| Más trabajo de setup | Hay que instalar en cada proyecto |
| Reinstalación | Puede ser repetitivo |

### Cuándo usar LOCAL

| Escenario | Por qué es mejor |
|-----------|------------------|
| Stacks diferentes | Proyecto Go vs proyecto Python |
| Sub-agentes específicos | Análisis de código en uno, revisión en otro |
| OpenSpec por proyecto | Cada proyecto tiene su estructura |
| Config personalizada | Convenciones propias |

---

## Tu situación

Tienes:
- Sub-agentes custom
- Diferentes stacks
- Quiere OpenSpec específico

| Componente | Recomendación |
|------------|--------------|
| Sub-agentes custom | **LOCAL** — no aplican a todos los proyectos |
| OpenSpec | **LOCAL** — cada proyecto tiene su stack |
| Skills universales | **GLOBAL** — conventional commits, testing |
| Persona | **GLOBAL** o **LOCAL** — según preferencia |

---

## Solución híbrida

```
GLOBAL (~/.config/opencode/)
├── Persona (Gentleman/Neutral)
├── Skills universales
│   ├── conventional-commits
│   ├── testing-patterns
│   └── docs-style
└── Skills compartidas

LOCAL (cada proyecto/)
├── .config/opencode/
│   ├── opencode.json (sub-agentes custom)
│   └── skills/
│       └── [skills específicas del stack]
├── openspec/
│   └── config.yaml
└── AGENTS.md (opcional)
```

---

## Regla de decisión

```
¿Este componente aplica a TODOS los proyectos?
├── SÍ → GLOBAL
└── NO → LOCAL
```

### Ejemplos

| Componente | ¿Global? | Por qué |
|------------|----------|---------|
| Conventional commits | ✅ Sí | Aplica a todos |
| Testing patterns | ✅ Sí | Aplica a todos |
| Persona | ✅ Sí | Aplica a todos |
| Skill de React | ❌ No | Solo proyectos React |
| Skill de Go | ❌ No | Solo proyectos Go |
| Sub-agente análisis | ❌ No | Solo si aplica |
| OpenSpec | ❌ No | Cada proyecto tiene su stack |

---

## Referencias

- [[subagentes-custom]] — Sub-agentes en OpenCode
- [[openspec]] — Sistema de archivos para SDD
- [[persona-y-profiles]] — Persona y Profiles
