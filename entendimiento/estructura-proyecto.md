# Estructura del Proyecto

## Árbol general

```
gentle-ai/
├── cmd/gentle-ai/        ← Entry point CLI (Go)
├── internal/             ← 34 paquetes del core
├── skills/               ← Skills del proyecto (11)
├── docs/                 ← Documentación extensa
├── bench/                ← Testing bench para journeys
├── scripts/              ← Scripts de instalación/release
├── e2e/                  ← Tests E2E en Docker (Ubuntu + Arch)
├── testdata/             ← Golden test fixtures
├── openspec/             ← SDD del propio proyecto
├── go.mod / go.sum       ← Dependencias Go
└── AGENTS.md             ← Índice de skills del proyecto
```

---

## internal/ — el core

| Paquete | Función |
|---------|---------|
| `app/` | Command dispatch + runtime wiring |
| `model/` | Domain types (agentes, componentes, skills, presets) |
| `catalog/` | Registry definitions |
| `system/` | OS/distro detection, platform guards |
| `cli/` | Install flags, validation, dry-run |
| `planner/` | Dependency graph, resolución, review payloads |
| `installcmd/` | Resolver de comandos por perfil (brew/apt/pacman...) |
| `pipeline/` | Ejecución por etapas + rollback |
| `backup/` | Config snapshot + restore |
| `assets/` | Skill files embebidos + plantillas de persona |
| `components/` | Lógica de install/inject por componente (engram, sdd, skills, mcp, persona, theme...) |
| `skillregistry/` | Skill registry refresh/list |
| `agents/` | Adapters de agentes (claude, opencode, gemini, cursor...) |
| `opencode/` | Utilidades de parsing de config OpenCode |
| `state/` | Tracking de estado de instalación |
| `update/` | Self-update + upgrade |
| `verify/` | Health checks post-apply |
| `tui/` | Bubbletea TUI (Rose Pine theme) |
| `reviewerprovider/` | Integración de review providers |
| `reviewtransaction/` | Lógica de transacciones de review |
| `sddstatus/` | Tracking de estado SDD |

---

## Punto de entrada

```
cmd/gentle-ai/          ← main() del CLI
```

Todo arranca desde ahí.

---

## Testing

```bash
# Unit tests
go test ./...

# E2E Docker (Ubuntu + Arch)
RUN_FULL_E2E=1 ./e2e/docker-test.sh

# Dry-run smoke
gentle-ai install --dry-run --agent claude-code --preset minimal
```

---

## Relación con otros proyectos

| Proyecto | Capa |
|----------|------|
| **Gentleman.Dots** | Dev environment (editors, shells) |
| **gentle-ai** | Capa de IA (agentes, memoria, skills) |

Son complementarios, no se solapan.

---

## Resumen

```
cmd/         → Entry point
internal/    → Core (34 paquetes)
skills/      → Skills del proyecto
docs/        → Documentación
bench/       → Test bench
e2e/         → Tests Docker
testdata/    → Golden fixtures
```

---

## Referencias

- [[01-arquitectura-general]] — Visión general
- [[skills]] — Sistema de skills
- [[plugins]] — Plugins de OpenCode
