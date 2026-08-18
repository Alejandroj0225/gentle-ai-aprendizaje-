# Skills - Cómo Crearlas y Dónde Ubicarlas

## Respuesta Corta

**Sí, puedes crear skills en múltiples ubicaciones** y el skill registry las detectará automáticamente.

---

## Ubicaciones donde el Skill Registry busca Skills

### User Skills (Globales - para todos los proyectos)

```
~/.config/opencode/skills/           ← OpenCode
~/.claude/skills/                   ← Claude Code
~/.cursor/skills/                   ← Cursor
~/.codex/skills/                    ← Codex
~/.pi/agent/skills/                ← Pi
~/.config/kilo/skills/             ← Kilo Code
~/.gemini/skills/                  ← Gemini CLI
~/.codeium/windsurf/skills/        ← Windsurf
~/.qwen/skills/                    ← Qwen Code
~/.kiro/skills/                    ← Kiro IDE
~/.openclaw/skills/                ← OpenClaw
~/.copilot/skills/                 ← VS Code Copilot
~/.config/agents/skills/
~/.agents/skills/
~/.kimi/skills/
```

### Project Skills (Locales del proyecto)

```
{project-root}/skills/
{project-root}/.opencode/skills/       ← .opencode/ (no .config/opencode/)
{project-root}/.claude/skills/
{project-root}/.cursor/skills/
{project-root}/.github/skills/
{project-root}/.pi/skills/
{project-root}/.agent/skills/
{project-root}/.agents/skills/
{project-root}/.atl/skills/
```

---

## ¿Y para tu caso específico?

### Si el orquestador es **global** (`~/.config/opencode/`)

```
~/.config/opencode/skills/           ← Skills aquí son detectadas
{project-root}/skills/               ← Project skills también detectadas
{project-root}/.opencode/skills/    ← NO detectadas si el orquestador es global
```

**Regla**: Las skills se buscan en `~/.config/opencode/skills/` (global) Y en `{project-root}/skills/` del proyecto.

### Si el orquestador es **local** (`{project-root}/.config/opencode/`)

```
{project-root}/.config/opencode/skills/  ← Skills aquí son detectadas
{project-root}/skills/                   ← Project skills también detectadas
```

---

## Estructura de una Skill

Cada skill es un **directorio** con `SKILL.md`:

```
skill-nombre/
├── SKILL.md              ← Obligatorio
├── references/          ← Opcional
│   ├── detail1.md
│   └── detail2.md
└── assets/              ← Opcional
    └── imagen.png
```

### Frontmatter obligatorio:

```yaml
---
name: nombre-de-skill
description: "Trigger: texto que activa la skill. Descripción breve."
disable-model-invocation: true
user-invocable: false
license: MIT
---
```

### Estructura del SKILL.md:

```markdown
## Execution Role
¿Soy el orchestrator o un executor?

## Activation Contract
¿En qué momento se activa esta skill?

## Hard Rules
Reglas absolutas que DEBO seguir.

## Decision Gates
| Si... | Entonces... |
|---|---|
| input A | acción B |

## Execution Steps
1. Paso 1
2. Paso 2

## Output Contract
¿Qué debo retornar?
```

---

## Cómo crea Gentle-AI las skills

Según `skill-creator`:

```
skill-creator/
├── SKILL.md              ← Skill para crear skills
└── references/
    ├── frontmatter-template.md
    └── skill-structure.md
```

El workflow de `skill-creator`:
1. Define el nombre y trigger
2. Genera el frontmatter
3. Pide estructura (Execution Role, Hard Rules, etc.)
4. Crea el directorio con SKILL.md

---

## Dónde crear skills para este proyecto

Dado que tienes:
- OpenCode local en `.config/opencode/`
- Un orquestador de Gentle-AI

**Opciones:**

### Opción A: Project skills

```
entendimiento/
├── skills/
│   └── mi-skill/
│       └── SKILL.md
└── .opencode/          ← (no existe)
```

### Opción B: En `.opencode/skills/` local

```
entendimiento/
├── .opencode/
│   └── skills/
│       └── mi-skill/
│           └── SKILL.md
└── skills/             ← Alternativa
    └── mi-skill/
        └── SKILL.md
```

### Opción C: Global (si el orquestador fuera global)

```
~/.config/opencode/skills/
```

---

## Recomendación para tu caso

Si quieres skills **solo para este proyecto de aprendizaje**:

```
entendimiento/
├── skills/
│   ├── mi-skill/
│   │   └── SKILL.md
│   └── otra-skill/
│       └── SKILL.md
```

El skill registry las detectará cuando ejecutes `gentle-ai skill-registry refresh`.

---

## Reglas de Deduplicación

- Si la **misma skill** existe en project Y en global → **project gana**
- Skills con prefijo `sdd-*` son ignoradas en el scan
- `_shared` y `skill-registry` son ignorados

---

## Comandos útiles

```bash
# Refrescar el skill registry
gentle-ai skill-registry refresh

# Ver skills detectadas
cat {project-root}/.atl/skill-registry.md
```

---

## Resumen

| Escenario | Dónde crear skills |
|-----------|-------------------|
| Orquestador global | `~/.config/opencode/skills/` |
| Orquestador local en proyecto | `{project-root}/.config/opencode/skills/` |
| Skills específicas del proyecto | `{project-root}/skills/` |
| Proyecto con .opencode/ | `{project-root}/.opencode/skills/` |

---

## Notas Relacionadas

- [[openspec]] — Sistema de archivos para SDD
- [[sdd]] — Spec-Driven Development
