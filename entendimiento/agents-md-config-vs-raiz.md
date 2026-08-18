# AGENTS.md en Config vs AGENTS.md en Raíz

## Resumen

| Ubicación | Scope | Contenido |
|-----------|-------|-----------|
| `.config/opencode/AGENTS.md` | **Configuración del agente OpenCode** | Instrucciones de sistema, prompts, configuración técnica |
| `RAÍZ/AGENTS.md` | **Convenciones del proyecto** | Reglas de equipo, workflow, stack, arquitectura |

---

## AGENTS.md en `.config/opencode/`

### ¿Qué es?
Es el archivo de **configuración del agente OpenCode** que gentle-ai genera automáticamente durante la instalación.

### ¿Qué contiene?

```markdown
<!-- gentle-ai:persona -->
## Rules          → Reglas de comportamiento del agente
## Personality    → Personalidad (Senior Architect)
## Language       → Idioma de respuesta
## Tone           → Tono de voz
## Philosophy      → Filosofía (CONCEPTS > CODE, etc.)
<!-- /gentle-ai:persona -->

<!-- gentle-ai:engram-protocol -->
## Engram Persistent Memory — Protocol
→ Instrucciones de cómo usar mem_save, mem_search, etc.
<!-- /gentle-ai:engram-protocol -->
```

### Características

- **No lo editas manualmente** — gentle-ai lo gestiona
- Se regenera con `gentle-ai sync`
- Si lo modificas, puede ser sobreescrito
- Contiene el **protocolo de Engram** que es mandatory

### ¿Quién lo usa?
El **agente OpenCode** cuando arranca una sesión.

---

## AGENTS.md en Raíz del Proyecto

### ¿Qué es?
Es el archivo de **convenciones del proyecto** que define reglas específicas para trabajar en ese proyecto.

### ¿Qué contiene?

```markdown
<!-- gentle-ai:project-conventions -->
## Reglas del Proyecto
→ No auto-push
→ Conventional commits
→ Stack: TypeScript + React

## Workflow
→ Siempre preguntar antes de crear nota
→ Guardar decisiones importantes en Engram
<!-- /gentle-ai:project-conventions -->
```

### Características

- **Tú lo editas** o yo lo genero cuando me lo indiques
- Gentle-ai **no lo sobreescribe** automáticamente
- Define reglas para **cualquier agente** que trabaje en el proyecto
- Especifica convenciones de equipo y arquitectura

### ¿Quién lo usa?
**Cualquier agente** que trabaje en el proyecto (OpenCode, Claude Code, Cursor, etc.)

---

## ¿Por qué dos archivos diferentes?

```
┌─────────────────────────────────────────────────────────────┐
│  .config/opencode/AGENTS.md                                │
│  → "Cómo debe comportarse el AGENTE OpenCode"              │
│  → Gent
```

Gentle-ai lo genera y mantiene                              │
└─────────────────────────────────────────────────────────────┘

```
┌─────────────────────────────────────────────────────────────┐
│  proyecto/AGENTS.md                                        │
│  → "Cómo trabajar en este PROYECTO"                        │
→ Convenciones específicas del equipo                        │
└─────────────────────────────────────────────────────────────┘
```

---

## Ejemplo de uso

### En un proyecto de aprendizaje (como este):

**.config/opencode/AGENTS.md** dice:
- "Usa el protocolo de Engram para memoria"
- "Sigue el skill registry para cargar skills"

**entendimiento/AGENTS.md** dice:
- "No hacer push automático"
- "Preguntar antes de crear notas"
- "Estructura de notas atómicas en bullets"

---

## ¿Cuál editar?

| Para cambiar... | Editar... |
|----------------|-----------|
| Comportamiento del agente | `.config/opencode/AGENTS.md` (via gentle-ai sync) |
| Convenciones del proyecto | `AGENTS.md` en raíz (manualmente) |
| Reglas de workflow | `AGENTS.md` en raíz |
| Stack o arquitectura | OpenSpec `config.yaml` |

---

## Tags de marcadores

Ambos usan **comentarios HTML** como marcadores:

```markdown
<!-- gentle-ai:persona -->
...contenido...
<!-- /gentle-ai:persona -->

<!-- gentle-ai:project-conventions -->
...contenido...
<!-- /gentle-ai:project-conventions -->
```

Esto permite a gentle-ai identificar y actualizar secciones específicas sin tocar el resto.

---

## Notas Relacionadas

- [[agentes-opencode]] — Sistema de agentes en OpenCode
- [[ gobernanza-reglas]] — Reglas de gobernanza del proyecto
