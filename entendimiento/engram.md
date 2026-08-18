# Engram — Memoria Persistente

## ¿Qué es Engram?

Es un **MCP (Model Context Protocol) server** que proporciona memoria persistente a los agentes de IA. No es solo "guardar notas" — es un sistema completo de gestión de memoria.

---

## Funcionalidades Principales

| Funcionalidad | Descripción | Cuándo usarla |
|---------------|-------------|---------------|
| `mem_save` | Guarda decisiones, bugs, descubrimientos | Después de cada decisión importante |
| `mem_search` | Busca en toda la memoria | Cuando necesitas recordar algo |
| `mem_context` | Recupera contexto de sesión reciente | Al inicio de sesión |
| `mem_session_summary` | Resumen de la sesión | Al final de sesión |
| `mem_update` | Actualiza una memoria existente | Cuando algo evoluciona |
| `mem_judge` | Resuelve conflictos entre memorias | Cuando hay duplicados |
| `mem_pin` | Pinnea una memoria importante | Para no perder algo crítico |

---

## Estructura de una Memoria

```go
type Memory struct {
    ID           int
    Title        string      // "Fixed N+1 query in UserList"
    Type         string      // bugfix | decision | architecture | pattern | config | preference
    Scope        string      // project | personal
    TopicKey     string      // "architecture/auth-model" (para updates)
    Content      string      // Contenido con **What**, **Why**, **Where**, **Learned**
    SessionID    string
    CreatedAt    time.Time
    ReviewAfter  time.Time
    State        string      // active | needs_review
}
```

---

## Topic Keys — Actualización Inteligente

Los **topic keys** permiten actualizar memorias del mismo tema sin crear duplicados:

```
Sin topic_key:
mem_save → "Decisión sobre auth" (nuevo)
mem_save → "Decisión sobre auth v2" (duplicado!)

Con topic_key:
mem_save topic_key="auth" → "Decisión sobre auth"
mem_save topic_key="auth" → ACTUALIZA la anterior
```

**Regla**: Diferentes topics = diferentes memorias. Mismo topic evolucionando = se actualiza.

---

## Flujo de Memoria en una Sesión

```
┌─────────────────────────────────────────────────────────────┐
│  SESIÓN INICIA                                              │
│  ↓                                                          │
│  mem_current_project() → Detecta proyecto actual            │
│  ↓                                                          │
│  mem_context() → Recupera contexto de sesiones recientes    │
│  mem_search() → Busca memorias relevantes si es necesario    │
│  ↓                                                          │
│  TRABAJO EN PROYECTO                                        │
│  ↓                                                          │
│  Decision/Bug/Descubrimiento                                │
│  ↓                                                          │
│  mem_save() → GUARDA la memoria                            │
│  ↓                                                          │
│  SESIÓN TERMINA                                             │
│  ↓                                                          │
│  mem_session_summary() → Resumen final de sesión           │
└─────────────────────────────────────────────────────────────┘
```

---

## Tipos de Memoria

| Type | Cuándo usarlo | Ejemplo |
|------|--------------|---------|
| `bugfix` | Arreglaste un bug | "Fixed N+1 query in UserList" |
| `decision` | Tomaste una decisión arquitectónica | "Chose JWT over session auth" |
| `architecture` | Descubriste algo de arquitectura | "OpenSpec va en raíz del proyecto" |
| `pattern` | Estableciste un patrón | "Usar commits conventional" |
| `config` | Cambiaste configuración | "Habilité strict_tdd" |
| `discovery` | Descubriste algo no obvio | "AGENTS.md no soporta anidamiento" |
| `preference` | Preferencia del usuario | "No hacer auto-push" |

---

## Scope — Project vs Personal

| Scope | Quién lo ve | Ejemplo |
|-------|-------------|---------|
| `project` | Solo este proyecto | "Stack de este proyecto" |
| `personal` | TODOS los proyectos | "No hacer auto-push" |

**Regla**: Reglas personales como `preference` siempre deben ser `scope: personal`.

---

## Conflictos de Memoria

Cuando `mem_save` detecta que podría haber conflicto, retorna `judgment_required: true`.

### Cuándo PREGUNTAR al usuario:

- confidence < 0.7
- relation = `supersedes` o `conflicts_with` Y type = `architecture` o `policy` o `decision`

### Cuándo RESOLVER en silencio:

- confidence >= 0.7 Y relation NO es `supersedes` ni `conflicts_with`
- relation = `related`, `compatible`, `scoped`, `not_conflict`

### Relations válidas:

| Relation | Significado |
|----------|-------------|
| `related` | Relacionada pero no choca |
| `compatible` | Compatible con la existente |
| `scoped` | Aplica a un scope diferente |
| `conflicts_with` | Choca con la existente |
| `supersedes` | Reemplaza a la existente |
| `not_conflict` | Evaluadas y no hay conflicto |

---

## Integración con Gentle-AI

Engram **viene preinstalado** con gentle-ai porque es fundamental:

1. **gentle-ai install** → configura Engram MCP en cada agente
2. **gentle-ai doctor** → verifica que Engram esté corriendo
3. **Protocolo en AGENTS.md** → el agente sabe que DEBE usar Engram

### Protocolo Obligatorio (del AGENTS.md):

```markdown
Call `mem_save` IMMEDIATELY after any of these:
- Architecture or design decision made
- Team convention documented
- Workflow change agreed upon
- Bug fix completed
- Non-obvious discovery about the codebase
- User preference or constraint learned

Before ending session:
mem_session_summary()
```

---

## Persistencia y Compactions

Engram sobrevive entre sesiones porque guarda en:

```
~/.engram/engram.db  → SQLite database
~/.engram/cloud.json → Sync config (si está configurado)
```

**Compaction**: Cuando el contexto se llena, Engram hace "compacción" — comprime memorias antiguas pero NO las pierde.

---

## Ejemplo de uso en Gentle-AI

### Después de una decisión:

```javascript
mem_save({
  title: "Chose Zustand over Redux",
  type: "decision",
  topic_key: "architecture/state-management",
  scope: "project",
  content: `
**What**: Replaced Redux with Zustand for state management
**Why**: Less boilerplate, simpler API, better TypeScript support
**Where**: src/store/, src/components/
**Learned**: Zustand doesn't have devtools middleware by default
  `
})
```

### Al final de sesión:

```javascript
mem_session_summary({
  content: `
## Goal
Learning Gentle-AI architecture

## Instructions
No auto-push - user controls git operations

## Discoveries
- OpenSpec is the SDD artifact storage system
- AGENTS.md in root vs .config/opencode/ have different scopes

## Accomplished
- Created governance rules
- Set up project AGENTS.md

## Next Steps
- Create notes for Engram, OpenSpec
- Continue exploring Gentle-AI architecture
  `
})
```

---

## Notas Relacionadas

- [[agents-md-config-vs-raiz]] — Diferencia entre AGENTS.md en config vs raíz
- [[ gobernanza-reglas]] — Reglas de gobernanza del proyecto
