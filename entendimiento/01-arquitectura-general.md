# Entendiendo Gentle-AI

> **Proyecto personal de estudio** — Documentación de aprendizaje sobre la arquitectura y funcionamiento de Gentle-AI.

## Visión General

**Gentle-AI no es un agente de IA** — es un **configurador de ecosistema**. Su trabajo es tomar los agentes de codificación AI que ya tienes instalados (Claude Code, OpenCode, Cursor, etc.) y dotarlos de:

1. **Memoria persistente** (Engram) — recuerda decisiones, bugs y contexto entre sesiones
2. **Workflow SDD** (Spec-Driven Development) — planificación antes de programar
3. **Skills** — patrones de mejores prácticas para React, TypeScript, testing, etc.
4. **MCP Servers** — Context7, Notion, Jira para acceder a documentación real
5. **Modelo de routing** — decide si hacer algo directo, delegarlo, o usar SDD
6. **Persona docente** — un agente que enseña, no solo completa
7. **Review bounded** — revisión de código con receipts (RDD)

## Arquitectura de Alto Nivel

```
┌─────────────────────────────────────────────────────────────────┐
│                    GENTLE-AI (Configurator)                      │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │   Engram     │  │     SDD      │  │    Skills System    │  │
│  │  (Memory)    │  │   (Planning)  │  │  (Best Practices)   │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
│                                                                  │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────────────┐  │
│  │     MCP      │  │     RDD      │  │   Organic Router    │  │
│  │  (Servers)   │  │  (Review)    │  │  (Implementation)   │  │
│  └──────────────┘  └──────────────┘  └──────────────────────┘  │
│                                                                  │
├─────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Se configura en: Claude Code, OpenCode, Cursor, Gemini CLI, etc. │
│                                                                  │
└─────────────────────────────────────────────────────────────────┘
```

## Conceptos Clave

### 1. Organic Routing (Enrutamiento Orgánico)

Gentle-AI decide automáticamente qué tipo de implementación usar:

| Situación | Ruta |
|-----------|------|
| 1-3 archivos, cambio mecánico ya entendido | **Direct inline** |
| 4+ archivos para explorar, o 2+ writes no triviales | **Delegated direct** |
| Propuesta durable, spec, design, tasks reducirían ambigüedad | **SDD (opcional)** |

> **Regla**: Tamaño, cantidad de archivos, o riesgo percibido NUNCA seleccionan SDD por sí solos — solo una solicitud explícita o una propuesta aceptada.

### 2. RDD (Receipt-Driven Development)

Es el sistema de **revisión de código** que:
- Congela el candidato (el código a revisar)
- Deriva esfuerzo de revisión de evidencia, nunca de tamaño
- Emite un **receipt** (recibo) que cada delivery gate valida
- **Es opt-in** — está apagado por defecto

Flujo RDD:
```
Implementación → Candidate congelado → Review (lenses) → Receipt (approved/correction/escalated)
```

### 3. SDD (Spec-Driven Development)

Workflow de 9 fases para cambios sustanciales:

```
sdd-explore → sdd-propose → sdd-spec → sdd-design → sdd-tasks
    ↓
sdd-apply → sdd-verify → sdd-archive
```

SDD es **opcional** y se ofrece solo cuando reduce ambigüedad sustancial.

### 4. Engram (Memoria Persistente)

- Servidor local en `localhost:7437`
- Base de datos SQLite + FTS5 en `~/.engram/engram.db`
- Plugins para Claude Code, OpenCode, Gemini CLI
- El agente recuerda decisiones, bugs, convenciones entre sesiones

### 5. Skills System

Skills son archivos `SKILL.md` que codifican mejores prácticas. Hay dos tipos:

- **gentle-ai-*** (repo-specific): `gentle-ai-branch-pr`, `gentle-ai-chained-pr`, etc.
- **Portable**: `comment-writer`, `work-unit-commits`, `cognitive-doc-design`, etc.

## Estructura del Proyecto

```
gentle-ai/
├── cmd/gentle-ai/          # Entry point CLI (Go)
├── internal/
│   ├── app/                 # Command dispatch + runtime wiring
│   ├── model/               # Domain types (agents, components, skills, presets, personas)
│   ├── catalog/             # Registry definitions (agents, skills, components)
│   ├── system/              # OS/distro detection, dependency checks, platform guards
│   ├── cli/                 # Install flags, validation, orchestration, dry-run
│   ├── planner/             # Dependency graph, resolution, ordering, review payloads
│   ├── installcmd/           # Profile-aware command resolver (brew/apt/pacman/dnf/winget/go install)
│   ├── pipeline/            # Staged execution + rollback orchestration
│   ├── backup/              # Config snapshot + restore
│   ├── assets/              # Embedded skill files + persona templates
│   ├── components/          # Per-component install/inject logic
│   │   ├── engram/  sdd/  skills/  mcp/  persona/  theme/  permissions/  gga/
│   │   ├── communitytool/   # Community tool install/guidance
│   │   └── filemerge/       # Marker-based file merging (inject without clobbering)
│   ├── skillregistry/       # .atl skill registry refresh/list support
│   ├── agents/              # Agent adapters (config strategy per agent)
│   │   ├── claude/  opencode/  gemini/  cursor/  vscode/  codex/  windsurf/  antigravity/
│   ├── state/               # Installation state tracking
│   ├── update/              # Self-update + upgrade logic
│   ├── tui/                 # Bubbletea TUI (Rose Pine theme)
│   ├── verify/              # Post-apply health checks + reporting
│   ├── reviewerprovider/     # Review provider integration
│   ├── reviewtransaction/   # Review transaction logic
│   └── sddstatus/           # SDD status tracking
├── skills/                  # Skills del proyecto (11 skills)
├── docs/                    # Documentación extensa
└── bench/                   # Testing bench para journeys
```

## Agentes Soportados

| Agente | Modelo de Delegación | Key Feature |
|--------|---------------------|-------------|
| Claude Code | Full (Task tool) | Sub-agents, output styles |
| OpenCode | Full (multi-mode) | Per-phase model routing |
| Cursor | Full (native subagents) | 10 SDD agents |
| Gemini CLI | Full (experimental) | Custom agents |
| Windsurf | Solo-agent | Plan Mode, Code Mode |
| Codex | Solo-agent | CLI-native, TOML config |
| Y más... | | |

## Flujo de Trabajo Diario

1. **Install**: `gentle-ai install` configura agentes seleccionados
2. **Use**: El agente usa Engram para memoria, Skills para patrones, MCP para docs
3. **Plan**: Para cambios sustanciales, el agente puede proponer SDD
4. **Commit**: GGA (Guardian Angel) revisa en pre-commit
5. **Review**: Si RDD está activo, hay review bounded antes de delivery

## Siguientes Pasos

- [ ] Explorar el código de `internal/cli/` para entender comandos
- [ ] Entender cómo funciona `internal/skillregistry/`
- [ ] Profundizar en `internal/rdd/` para entender review receipts
- [ ] Ver cómo `internal/opencode/` maneja perfiles SDD
- [ ] Documentar el sistema de agentes en `internal/agents/`

---

*Este documento se actualiza conforme se profundiza en el entendimiento.*
