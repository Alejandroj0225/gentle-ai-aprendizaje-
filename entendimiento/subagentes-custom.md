# Sub-Agentes en OpenCode — Mi Situación

## Contexto

Tengo dos configuraciones de OpenCode:

### 1. Mi configuración personal (en otro proyecto)
```
.opencode/
└── agents/
    ├── mi-analisis.md
    ├── mi-revisor.md
    └── otro-agente.md
```

### 2. Gentle Orchestrator (este proyecto)
```
.config/opencode/
└── opencode.json
```

---

## Problema

**Gentle Orchestrator NO detecta los sub-agentes que tengo en `.opencode/agents/`.**

El Gentle Orchestrator solo lee los agentes definidos en `opencode.json` — no hace un merge automático con archivos en `.opencode/agents/`.

---

## Solución

Para usar mis sub-agentes con Gentle Orchestrator, debo migrarlos a `opencode.json`:

```json
{
  "agent": {
    // Agentes de Gentle AI...
    "gentle-orchestrator": { ... },
    "sdd-apply": { ... },
    
    // Mis sub-agentes custom...
    "mi-analisis": {
      "description": "Análisis de código",
      "mode": "subagent",
      "tools": { "read": true, "bash": true },
      "prompt": "Eres un agente de análisis..."
    },
    "mi-revisor": {
      "description": "Revisor custom",
      "mode": "subagent", 
      "tools": { "read": true, "edit": true },
      "prompt": "Eres un revisor..."
    }
  }
}
```

---

## Riesgos con Gentle Orchestrator

### `gentle-ai install`

| Riesgo | Qué pasa |
|--------|----------|
| ⚠️ Sobrescribe `opencode.json` | Puede perder mis sub-agentes custom |
| ⚠️ Reinstalación completa | Borra todo y reconfigura desde cero |

**Mitigación:** Hacer backup antes de ejecutar `install`

```bash
cp .config/opencode/opencode.json .config/opencode/opencode.json.backup
gentle-ai install
```

### `gentle-ai sync`

| Riesgo | Qué pasa |
|--------|----------|
| ✅ Merge inteligente | Preserva claves no-SDD |
| ✅ No sobrescribe | Solo actualiza assets de Gentle AI |

**Seguro** — no hay riesgo de perder sub-agentes custom.

---

## Reglas de Operación

| Comando | Seguro? | Acción previa |
|---------|---------|--------------|
| `gentle-ai sync` | ✅ Sí | Ninguna |
| `gentle-ai install` | ⚠️ No | **Backup obligatorio** |

---

## Alternativa Futura

Si quiero mantener sub-agentes en archivos markdown separados, necesitaría:
- Una herramienta que los compile en `opencode.json`
- O esperar a que Gentle AI soporte lectura de `.opencode/agents/`

**Por ahora:** La única opción es tenerlos directamente en `opencode.json`.

---

## Nota importante sobre sync

`gentle-ai sync` hace **deep merge**:
- Actualiza solo las claves SDD (`sdd-*`)
- **Preserva** todas las demás claves
- Mis sub-agentes (`mi-*`) no se tocan

---

## Referencias

- [[persona-y-profiles]] — Persona vs Profiles
- [[skills]] — Sistema de skills
