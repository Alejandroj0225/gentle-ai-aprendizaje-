# Agentes Soportados

## Lista completa de agentes

Gentle-AI configura 16+ agentes de codificación AI:

| Agente | ID | Delegación | Config Path |
|--------|-----|-----------|-------------|
| Claude Code | `claude-code` | Full (Task tool) | `~/.claude` |
| OpenCode | `opencode` | Full (multi-mode overlay) | `~/.config/opencode` |
| Kilo Code | `kilocode` | Full (multi-mode overlay) | `~/.config/kilo` |
| Gemini CLI | `gemini-cli` | Full (experimental) | `~/.gemini` |
| Cursor | `cursor` | Full (native subagents) | `~/.cursor` |
| VS Code Copilot | `vscode-copilot` | Full (runSubagent) | `~/.copilot` |
| Codex | `codex` | Native multi-agent | `~/.codex` |
| Windsurf | `windsurf` | Solo-agent | `~/.codeium/windsurf` |
| Antigravity | `antigravity` | Solo + Mission Control | `~/.gemini/antigravity` |
| Kimi Code | `kimi` | Full (custom agents) | `~/.kimi` |
| Qwen Code | `qwen-code` | Full (sub-agents) | `~/.qwen` |
| Kiro IDE | `kiro-ide` | Full (native subagents) | `~/.kiro` |
| OpenClaw | `openclaw` | Solo-agent | `~/.openclaw` |
| Trae | `trae-ide` | Solo-agent | `~/.trae` |
| Pi | `pi` | Full (package-managed) | `~/.pi` |
| Hermes | `hermes` | Full (delegate_task) | `~/.hermes` |

---

## Modelos de delegación

| Modelo | Cómo funciona | Agentes |
|--------|--------------|---------|
| **Full (sub-agents)** | Cada fase SDD corre en contexto aislado vía sub-agentes nativos | Claude Code, OpenCode, Kilo, Gemini, Cursor, Copilot, Kimi, Kiro, Qwen, Pi |
| **Full (delegate_task)** | Workers efímeros con misión autocontenida | Hermes |
| **Native multi-agent** | Herramientas nativas de colaboración, fallback inline | Codex |
| **Solo-agent** | Todas las fases inline en la misma conversación | Windsurf, Antigravity, OpenClaw, Trae |

---

## Multi-mode SDD (modelos por fase)

| ¿Soporta multi-mode? | Agentes |
|:---:|---|
| ✅ Sí | **OpenCode**, **Kilo Code**, **Kiro IDE**, **Pi** |
| ❌ No (single-mode) | El resto — el orquestador usa el modelo activo |

**Multi-mode** = asignar diferentes modelos de IA a cada fase SDD (modelo potente para design, barato para spec...).

---

## Instalación

```bash
# Todos los agentes detectados
gentle-ai install

# Agente específico
gentle-ai install --agents opencode

# Solo este proyecto
gentle-ai install --scope=workspace --agents opencode
```

---

## Notas por agente

### Claude Code
- Sub-agents vía Task tool
- MCP como plugins en `~/.claude/mcp/`
- System prompt en `~/.claude/CLAUDE.md`

### OpenCode
- Overlay multi-agente: `gentle-orchestrator` + 10 fases SDD en `opencode.json`
- Slash commands: `/sdd-new`, `/sdd-explore`...
- **Multi-mode** con Tab para cambiar perfiles

### Cursor
- 10 archivos en `~/.cursor/agents/sdd-{phase}.md`
- Auto-delegación según `description` del frontmatter

### Windsurf
- Solo-agent (sin sub-agentes custom)
- Plan Mode / Code Mode / Workflows nativos

### Codex
- Config TOML en `~/.codex/config.toml`
- Profiles de modelos separados (`~/.codex/<name>.config.toml`)
- Multi-agent con fallback solo-agent

---

## Resumen

```
Gentle-AI configura CUALQUIER agente que uses
├── 16+ agentes soportados
├── Modelos de delegación: full / solo / nativo
├── 4 agentes con multi-mode SDD (modelos por fase)
└── scope: global o workspace
```

---

## Referencias

- [[subagentes-custom]] — Sub-agentes custom en OpenCode
- [[instalacion-global-vs-local]] — Global vs local
- [[01-arquitectura-general]] — Visión general
