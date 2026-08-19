# Análisis — Integración de Gentle-AI en la Bóveda de Obsidian

## Contexto

Análisis de la posibilidad de integrar el framework Gentle-AI (gentle-orchestrator) en la bóveda personal de Obsidian (`C:\Users\alela\obsidian\ALEJANDRO J LOPEZ`), conservando los flujos de trabajo actuales.

---

## 1. Estructura actual de la bóveda

| Componente | Cómo está | Ubicación |
|-----------|-----------|-----------|
| **Orquestador** | 1 agente primario custom | `.opencode/agents/orquestador.md` |
| **Subagentes** | 6 markdown (scc-plan, scc-verify, gestor-sistemas, etc.) | `.opencode/agents/*.md` |
| **Skills** | 9 carpetas con matriz externalizada | `.agents/skills/` |
| **AGENTS.md** | Muy elaborado: gobernanza, metas, alcances, personalidades | raíz + locales de meta |
| **SCC** | Ciclo de Cambio Formal que adapta SDD/TDD | `Sistemas/.../Sistema - Ciclo de Cambio Formal (SCC).md` |
| **opencode.json** | En la raíz (skills.paths + MCP propios) | `opencode.json` |
| **Engram** | Sin base local — usa la GLOBAL | `~/.engram/engram.db` |

---

## 2. Hallazgo clave: Engram es GLOBAL

**Engram NO está en el `opencode.json` de la bóveda** (solo gemini-image-gen, higgsfield, playwright). La base es **global única**: `~/.engram/engram.db`.

```
~/.engram/engram.db    ← LA base (global, una sola)
Bóveda: no tiene .engram local
Proyecto "Entendiendo gentle ia": tiene .engram/ (config local del plugin)
```

**Respuesta sobre Engram:**
- Engram usa **una sola base global** (`~/.engram/engram.db`)
- Los proyectos se separan por el campo `project` al guardar, NO por bases distintas
- Bóveda y "Entendiendo gentle ia" comparten la misma base, con memorias etiquetadas por proyecto
- **Instalar gentle-ai NO reinstala Engram** — ya está global. Solo añade la MCP config al `opencode.json`

---

## 3. Análisis de compatibilidad por componente

### Skills — ✅ COMPATIBLES

El skill registry de gentle-ai escanea `.agents/skills/` (está en su lista de ProjectSkillDirs). **Las skills de la bóveda serían detectadas automáticamente.**

```
{project-root}/.agents/skills/   ← gentle-ai lo escanea ✅
```

### Subagentes — ✅ NO SE PIERDEN (matiz)

**Corrección importante:** OpenCode lee los agentes en `.opencode/agents/*.md` de forma nativa. **No hace falta moverlos a `opencode.json`.** Los subagentes custom siguen funcionando tal como están.

**El matiz:** el `gentle-orchestrator` por defecto solo delega a sus subagentes SDD. Para que también llame a los subagentes custom (scc-plan, scc-verify, gestores), hay que añadirlos a sus permisos en `opencode.json`.

### Engram — ✅ NO HAY CONFLICTO

Base global única, no se reinstala, no se duplica.

### Persona — ⚠️ USAR `--persona custom`

`--persona custom` = "no inyectes ninguna persona, mantén la mía". No sobrescribe el tono/instrucciones del usuario.

---

## 4. Aclaraciones críticas

### La persona NO es un orquestador

| Concepto | Qué es | Dónde vive |
|----------|--------|-----------|
| **Persona** | El TONO (Gentleman/Neutral/Custom) | `AGENTS.md` |
| **Orquestador** | QUÉ hace el agente (coordinar SDD, enrutar) | `opencode.json` |

`--persona custom` NO es un lugar donde pegar instrucciones — es un modo de "no interferir".

### Las instrucciones de meta+alcance NO son persona

El sistema de enrutamiento (meta → alcance → personalidad → skills) es comportamiento del **orquestador**, no del tono.

### ⚠️ El AGENTS.md de la raíz NO se sobrescribe

Verificado en el código del adapter de OpenCode:

```go
// internal/agents/opencode/adapter.go
func (a *Adapter) SystemPromptFile(homeDir string) string {
    return filepath.Join(ConfigPath(homeDir), "AGENTS.md")  // ← .config/opencode/AGENTS.md
}
```

Gentle-AI escribe su system prompt en **`.config/opencode/AGENTS.md`** (archivo distinto).

```
Bóveda/
├── AGENTS.md                  ← TUYO, intacto (gobernanza, meta+alcance)
└── .config/opencode/
    └── AGENTS.md              ← Gestionado por gentle-ai (system prompt)
```

**No hace falta guardar/pegar nada.** El AGENTS.md de gobernanza queda intacto.

### default_agent

Al instalar, gentle-ai pone en `opencode.json`:

```json
{
  "default_agent": "gentle-orchestrator"
}
```

Eso significa: al abrir OpenCode, el agente activo será `gentle-orchestrator`. Como se elimina el orquestador custom, el enrutamiento por meta+alcance debe ser seguido por `gentle-orchestrator` leyendo el AGENTS.md raíz.

---

## 5. Cómo conservar el enrutamiento por meta+alcance

| Sistema actual | Dónde está | Al instalar gentle-ai |
|----------------|-----------|----------------------|
| Enrutamiento (meta+alcance) | `.opencode/agents/orquestador.md` + AGENTS.md raíz | Se elimina el orquestador |
| Personalidades por alcance | `Sistemas/.../Personalidades/` | Se mantienen (archivos) |
| Reglas de gobernanza | AGENTS.md raíz | **Intactas** |
| Skills | `.agents/skills/` | Detectadas por skill registry |

Si se elimina el orquestador custom, el enrutamiento por meta+alcance **deja de ejecutarse** a menos que:
1. Las reglas estén en el AGENTS.md raíz (ya están, sección "Modelo operativo vigente")
2. `gentle-orchestrator` las siga como contexto del proyecto

---

## 6. Recomendación: integración selectiva

No es "todo o nada". Se puede integrar solo lo que aporta:

| Componente de gentle-ai | ¿Integrarlo? | Cómo |
|------------------------|:---:|------|
| **Skill registry** | ✅ Sí | Escanea `.agents/skills/` — no rompe nada |
| **Engram MCP** | ✅ Sí | Ya funciona global — solo añade MCP config |
| **Persona** | ⚠️ Opcional | `--persona custom` para no sobreescribir |
| **SDD orchestrator** | ⚠️ Con cuidado | Reemplaza el orquestador custom |
| **RDD** | ❌ No | El SCC ya emula el patrón sin el aparato de RDD |

### Comando seguro de instalación

```bash
# Desde la bóveda — workspace, sin persona, sin tocar gobernanza
gentle-ai install --scope=workspace --agents opencode --persona custom
```

---

## 7. Orden seguro de operación

```
1. Backup de TODO (AGENTS.md raíz, .opencode/agents/, Personalidades/)
2. (Opcional) Ajustar AGENTS.md raíz: quitar referencias al orquestador eliminado
3. Instalar:
   gentle-ai install --scope=workspace --agents opencode --persona custom
4. Verificar que AGENTS.md raíz sigue intacto
5. Añadir permisos a los subagentes custom en opencode.json
6. Sync → verificar que nada se rompió
```

---

## 8. Riesgos resumidos

| Riesgo | ¿Pasa? |
|--------|--------|
| Se pierden las skills | ❌ No — skill registry las detecta |
| Se pierden los subagentes markdown | ❌ No — OpenCode los lee nativamente |
| Se reinstala/rompe Engram | ❌ No — base global única |
| Se sobrescribe el AGENTS.md raíz | ❌ No — gentle-ai usa `.config/opencode/AGENTS.md` |
| Se pierde el enrutamiento meta+alcance | ⚠️ Sí, si se elimina el orquestador sin mover las reglas |
| default_agent cambia a gentle-orchestrator | ⚠️ Esperado — el agente activo al abrir OpenCode |

---

## Referencias

- [[engram]] — Memoria persistente (base global)
- [[skills]] — Skill registry escanea `.agents/skills/`
- [[subagentes-custom]] — Subagentes en `.opencode/agents/*.md`
- [[sdd]] — SDD nativo de gentle-ai
- [[persona-y-profiles]] — Persona vs orquestador
- [[ejecutar-gentle-ai]] — Scope e instalación
- [[instalacion-global-vs-local]] — Global vs workspace
- [[agents-md-config-vs-raiz]] — AGENTS.md config vs raíz
