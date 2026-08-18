# Commands vs Skills — Cómo trabajan juntos

## Son complementarios

```
Command (/sdd-new)
  └─→ Lanza Skill (sdd-explore)
  └─→ Lanza Skill (sdd-propose)
  └─→ Espera input del usuario
  └─→ Lanza Skill (sdd-spec)
```

---

## Diferencias

| Aspecto | Command | Skill |
|---------|---------|-------|
| **Quién invoca** | Tú (`/comando`) | El agente (automático) |
| **Propósito** | Orquestar flujos | Definir patrones de ejecución |
| **Qué hace** | Coordina múltiples pasos | Instrucciones detalladas |
| **Ejecutor** | Tú escribes `/comando` | Agente detecta trigger |

---

## Ejemplo concreto

### Command: `/sdd-new "auth"`

```markdown
---
description: Start a new SDD change
agent: gentle-orchestrator
---

1. Launch sdd-explore sub-agent
2. Present exploration summary
3. Launch sdd-propose sub-agent
4. Ask user: ¿continuar?
```

### Skill: `sdd-explore`

```yaml
---
name: sdd-explore
description: "Trigger: sdd explore..."
---

## Hard Rules
- Never guess stack
- Read files completely before conclusions

## Decision Gates
| mode | action |
| openspec | write to openspec/specs/ |

## Execution Steps
1. Inspect codebase
2. Analyze patterns
3. Return findings
```

---

## Trabajan en el mismo flujo

```
┌─────────────────────────────────────────────────────────────┐
│  SDD Workflow                                              │
│                                                              │
│  /sdd-new "auth"     ← Command (tú lo invocas)            │
│       ↓                                                     │
│  sdd-explore          ← Skill (agente ejecuta)            │
│       ↓                                                     │
│  sdd-propose         ← Skill (agente ejecuta)            │
│       ↓                                                     │
│  sdd-spec            ← Skill (agente ejecuta)            │
│       ↓                                                     │
│  sdd-design          ← Skill (agente ejecuta)            │
│       ...                                                   │
└─────────────────────────────────────────────────────────────┘
```

---

## Resumen

| Concepto | Qué es | Quién lo activa |
|----------|--------|----------------|
| **Command** | Orquestador de flujo | Tú (`/sdd-new`) |
| **Skill** | Instrucciones de ejecución | Agente (automático) |

---

## Analogía

```
Command = "Receta completa"
Skill = "Paso específico de la receta"

Command dice: "Haz la salsa"
  └─→ Skill dice: "Cómo hacer la salsa: sal, aceite, ingredientes"
```

---

## Estructura en el filesystem

```
.config/opencode/
├── commands/           ← Commands (slash commands)
│   ├── sdd-new.md
│   ├── sdd-init.md
│   ├── sdd-apply.md
│   └── sdd-verify.md
│
└── skills/            ← Skills (patrones)
    ├── sdd-new/
    │   └── SKILL.md
    ├── sdd-apply/
    │   └── SKILL.md
    └── sdd-verify/
        └── SKILL.md
```

---

## Casos de uso

| Necesitas... | Usa... |
|-------------|--------|
| Iniciar un flujo SDD | `/sdd-new` (Command) |
| Inicializar proyecto | `/sdd-init` (Command) |
| Aplicar cambios | `/sdd-apply` (Command) |
| Que el agente siga un patrón | Skill |
| Que el agente sepa cómo ejecutar | Skill |

---

## Nota importante

Skills pueden ser invocados:
1. **Automáticamente** por el agente (cuando detecta trigger)
2. **Manualmente** por Command (que los orquesta)

Commands siempre son invocados por ti.

---

## Referencias

- [[skills]] — Sistema de skills
- [[openspec]] — Sistema de archivos para SDD
- [[subagentes-custom]] — Sub-agentes en OpenCode
