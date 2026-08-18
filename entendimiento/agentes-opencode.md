# Agentes en OpenCode - Sistema de AGENTS.md y opencode.json

## Descubrimiento

Al instalar gentle-ai con `--scope=workspace --agents opencode`, se creó un archivo `AGENTS.md` en `.config/opencode/`. Este archivo contiene las instrucciones de sistema que OpenCode usa para configurar el agente.

## Arquitectura de Agentes en OpenCode

En OpenCode (y muchos otros agentes), gentle-ai inyecta configuración a través de **dos archivos principales**:

### 1. `AGENTS.md` - Instrucciones de Sistema

Contiene markdown con secciones marcadas por comentarios HTML:

```markdown
<!-- gentle-ai:persona -->
## Rules
## Personality
## Language
## Tone
## Philosophy
...
<!-- /gentle-ai:persona -->

<!-- gentle-ai:engram-protocol -->
## Engram Persistent Memory — Protocol
...
<!-- /gentle-ai:engram-protocol -->
```

**Secciones típicas:**
- `gentle-ai:persona` → Configuración de la persona (cómo habla el agente)
- `gentle-ai:engram-protocol` → Protocolo de memoria persistente
- `gentle-ai:sdd-orchestrator` → Instrucciones de orquestación SDD
- `gentle-ai:agent-language-contract` → Reglas de lenguaje para artefactos

### 2. `opencode.json` - Definición de Agentes y Herramientas

Define agentes como objetos JSON con permisos, prompts y herramientas:

```json
{
  "agent": {
    "gentle-orchestrator": {
      "description": "...",
      "mode": "primary",
      "prompt": "...",
      "tools": { "task": true, "read": true, ... }
    },
    "sdd-apply": {
      "description": "...",
      "mode": "subagent",
      "prompt": "...",
      "tools": { "edit": true, "write": true, ... }
    }
  }
}
```

## Modelo de Agentes

| Agente | Rol | Descripción |
|--------|-----|-------------|
| `gentle-orchestrator` | **Primary** | Coordenador SDD - delega todo el trabajo |
| `sdd-apply` | Subagent | Ejecuta implementación |
| `sdd-verify` | Subagent | Valida contra specs |
| `sdd-explore` | Subagent | Investiga el codebase |
| `sdd-propose` | Subagent | Crea propuestas |
| `sdd-spec` | Subagent | Escribe especificaciones |
| `sdd-design` | Subagent | Diseño técnico |
| `sdd-tasks` | Subagent | Desglosa en tareas |
| `sdd-archive` | Subagent | Archiva cambios completados |
| `jd-judge-a` | Subagent | Revisor adversarial A (Judgment Day) |
| `jd-judge-b` | Subagent | Revisor adversarial B (Judgment Day) |
| `review-risk` | Subagent | Revisor de seguridad/riesgo |
| `review-readability` | Subagent | Revisor de legibilidad |
| `review-reliability` | Subagent | Revisor de confiabilidad |
| `review-resilience` | Subagent | Revisor de resiliencia |
| `review-validator` | Subagent | Validador de revisión |

## Preguntas Respondidas

### ¿Solo puede haber un AGENTS.md?

No necesariamente. Los archivos `AGENTS.md` son convenciones de varios agentes:

| Agente | Archivo de Sistema Prompt |
|--------|--------------------------|
| Claude Code | `CLAUDE.md` |
| OpenCode | `AGENTS.md` |
| Codex | `AGENTS.md` |
| Windsurf | `SOUL.md` |
| Cursor | `.cursorrules` |

El archivo `AGENTS.md` en `.config/opencode/` es **el archivo de sistema principal** para este proyecto.

### ¿Qué tan anidado puede estar?

La estructura es **plana** para OpenCode:
- Un `AGENTS.md` principal con secciones marcadas por comentarios HTML
- Múltiples agentes definidos en `opencode.json`
- Los agentes **NO se anidan** - son referencias planas

### ¿Puedo tener varios AGENTS.md en diferentes ubicaciones?

Depende del agente. En el caso de OpenCode con `--scope=workspace`:
- El `AGENTS.md` está en `.config/opencode/` (configuración global del workspace)
- Si abres un proyecto específico, puede haber otro `AGENTS.md` o `CLAUDE.md` en ese proyecto

---

## Referencias

- `.config/opencode/AGENTS.md` - Archivo de sistema con固执ona y protocolo Engram
- `.config/opencode/opencode.json` - Definición de agentes y configuración de herramientas
