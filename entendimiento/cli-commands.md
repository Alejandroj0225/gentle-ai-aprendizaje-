# Comandos CLI

## Comandos principales

| Comando | Función |
|---------|---------|
| `gentle-ai install` | Instala/configura agentes y componentes |
| `gentle-ai sync` | Refresca assets gestionados (merge seguro) |
| `gentle-ai upgrade` | Actualiza el binario |
| `gentle-ai doctor` | Health check del ecosistema (read-only) |
| `gentle-ai version` | Muestra versión |
| `gentle-ai skill-registry refresh` | Escanea skills y reconstruye el registry |
| `gentle-ai review mode status/enable/disable` | Controla RDD (opt-in) |
| `gentle-ai review start` | Inicia revisión de un candidato |
| `gentle-ai review inspect-candidate` | Inspecciona candidato inmutable |
| `gentle-ai sdd-status` | Estado del SDD |

---

## Instalación

```bash
# Instalación interactiva (TUI)
gentle-ai install

# Sin interacción, agentes específicos
gentle-ai install --agents claude-code,opencode --preset gentleman

# Solo este proyecto (workspace)
gentle-ai install --scope=workspace --agents opencode

# Dry-run (no aplica nada)
gentle-ai install --dry-run --agent claude-code --preset minimal
```

### Scope

| Scope | Dónde escribe | Uso |
|-------|--------------|-----|
| `global` (default) | `~/.config/...` | Todos los proyectos |
| `workspace` | `.config/` en el proyecto | Solo este proyecto |

---

## Sync (SEGURO)

```bash
gentle-ai sync
```

- Refresca assets gestionados (prompts, skills, personas)
- Hace **merge** — preserva claves no gestionadas (sub-agentes custom)
- Ejecutar después de actualizar el binario

---

## Upgrade

```bash
gentle-ai upgrade   # actualiza el binario
gentle-ai sync      # después, refresca assets
```

---

## Doctor (read-only)

```bash
gentle-ai doctor
```

Verifica:
- Binarios de agentes instalados
- `state.json`
- Engram reachability
- Espacio en disco

---

## Skill registry

```bash
gentle-ai skill-registry refresh
```

- Escanea skills (user + project)
- Construye `.atl/skill-registry.md`
- Se ejecuta automáticamente al inicio de OpenCode (plugin)

---

## Review (RDD)

```bash
gentle-ai review mode status --cwd .
gentle-ai review mode enable --scope global --cwd .
gentle-ai review mode disable --cwd .
gentle-ai review start --projection staged
```

---

## Resumen

```
install  → configura (⚠️ backup antes si ya tienes custom)
sync     → refresca (✅ seguro, merge)
upgrade  → actualiza binario
doctor   → diagnostica (read-only)
skill-registry refresh → reconstruye registry
review   → controla RDD
```

---

## Referencias

- [[estructura-proyecto]] — Organización del código
- [[sdd-init]] — Inicialización del contexto SDD
- [[rdd]] — Receipt-Driven Development
- [[instalacion-global-vs-local]] — Global vs local
