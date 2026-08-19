# Plan de Migración — De Orquestador Custom a Gentle-Orchestrator

> Plan detallado para migrar la bóveda de Obsidian de su orquestador custom (`.opencode/agents/orquestador.md`) al `gentle-orchestrator` de Gentle-AI, en modo **local (workspace)**.

---

## 0. Decisión de arquitectura (resumen)

| Elemento | Decisión |
|----------|----------|
| Orquestador custom | **ELIMINAR** → usar `gentle-orchestrator` |
| Subagentes `.opencode/agents/*.md` | **CONSERVAR** → añadir permisos al orquestador |
| SCC (Ciclo de Cambio Formal) | **ADAPTAR** → ver Fase 4 (matiz importante) |
| AGENTS.md raíz (gobernanza) | **CONSERVAR** intacto |
| Personalidades | **CONSERVAR** sin cambios |
| Skills de la bóveda | **CONSERVAR** → ver Fase 5 |
| Engram | **CONSERVAR** (base global, sin cambios) |

---

## 1. Fase 0: Backup completo (OBLIGATORIO)

Antes de tocar nada:

```powershell
# Backup de los archivos críticos
$fecha = Get-Date -Format "yyyy-MM-dd"
$dest = "C:\Users\alela\obsidian\backup-migracion-$fecha"
New-Item -ItemType Directory -Path $dest -Force

# Copiar piezas críticas
Copy-Item "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\AGENTS.md" $dest -Force
Copy-Item -Recurse "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\.opencode" $dest -Force
Copy-Item -Recurse "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\.agents" $dest -Force
Copy-Item -Recurse "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\Sistemas\Sistemas de funcionamiento de la bóveda" $dest -Force
```

✅ **Verificación:** Confirmar que `backup-migracion-$fecha` contiene todo antes de continuar.

---

## 2. Fase 1: Ajustar el AGENTS.md raíz (antes de instalar)

El AGENTS.md raíz **NO se sobrescribe** (gentle-ai usa `.config/opencode/AGENTS.md`), pero contiene referencias al orquestador custom que quedarán obsoletas.

**Editar en `AGENTS.md` raíz:**

1. Buscar todas las referencias a `.opencode/agents/orquestador.md` (sección "Modelo operativo vigente", "Ubicación de AGENTS.md")
2. Reemplazar la mención "operado exclusivamente a través del orquestador (único agente primario)" por "operado a través del `gentle-orchestrator`"
3. Actualizar "Orquestación de subagentes SCC" → indicar que la tabla ahora la sigue `gentle-orchestrator`
4. Conservar TODO lo demás (gobernanza, metas, alcances, protocolo Engram, skills)

> ⚠️ **No eliminar el protocolo Engram del AGENTS.md raíz** — se mantiene como fuente única a nivel de bóveda (el de `.config/opencode/AGENTS.md` es el del system prompt).

---

## 3. Fase 2: Instalación de Gentle-AI (local)

Desde la **raíz de la bóveda**:

```powershell
# Pararse en la bóveda
cd "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ"

# Instalar OpenCode en workspace, SIN persona (custom = no tocar la mía)
gentle-ai install --scope=workspace --agents opencode --persona custom
```

**Qué hace:**
- ✅ Crea `.config/opencode/opencode.json` con `gentle-orchestrator` + subagentes SDD
- ✅ Crea `.config/opencode/AGENTS.md` (system prompt con persona custom + engram protocol)
- ✅ Copia skills SDD a `.config/opencode/skills/`
- ✅ Configura MCP de Engram
- ❌ NO toca tu `AGENTS.md` raíz
- ❌ NO toca `.opencode/agents/`
- ❌ NO toca `.agents/skills/`

**⚠️ Riesgo a vigilar:** `gentle-ai install` en workspace podría intentar escribir en el `opencode.json` de la RAÍZ (el que ya tienes con skills.paths y MCP). Verificar tras la instalación que tu `opencode.json` raíz sigue intacto, o que el merge preservó tus MCP (gemini-image-gen, higgsfield, playwright).

---

## 4. Fase 3: Dar permisos a los subagentes custom

Tras la instalación, editar `.config/opencode/opencode.json` (el nuevo de gentle-ai) y añadir tus subagentes al `gentle-orchestrator`:

```json
{
  "agent": {
    "gentle-orchestrator": {
      "permission": {
        "task": {
          "*": "deny",
          "sdd-apply": "allow",
          "sdd-verify": "allow",
          "scc-plan": "allow",
          "scc-verify": "allow",
          "gestor-sistemas": "allow",
          "gestor-horizonte-conocimiento": "allow",
          "gestor-aprendizajes-temporales": "allow",
          "gestor-brainstorming": "allow"
        }
      }
    }
  }
}
```

**Verificar tras sync:** como `gentle-ai sync` hace deep-merge y preserva claves no-SDD, los permisos a `scc-*` y `gestor-*` deberían mantenerse.

---

## 5. Fase 4: Decisión SCC → SDD nativo (⚠️ MATIZ CRÍTICO)

El usuario quiere reemplazar el SCC por el SDD nativo. **Hay un matiz importante:**

| Verificación | SDD nativo de gentle-ai | SCC de la bóveda |
|-------------|------------------------|-------------------|
| Contra specs de código | ✅ Sí (tests) | ❌ No aplica |
| Wikilinks / índices | ❌ NO lo hace | ✅ Sí |
| Nomenclatura | ❌ NO lo hace | ✅ Sí |
| Referencias rotas | ❌ NO lo hace | ✅ Sí |
| Alineación con gobernanza | ❌ NO lo hace | ✅ Sí |

**El SDD nativo NO reemplaza la verificación cualitativa de la bóveda** (wikilinks, índices, nomenclatura, alineación). Esa es la "check suite contextual" que vive en `scc-verify`.

### Recomendación (híbrido):

```
SDD nativo (gentle-orchestrator)     → flujo formal: explore→propose→spec→design→tasks→apply
scc-verify (subagente custom)        → verificación final cualitativa de la bóveda
```

Es decir:
- **Para el flujo de planificación:** usar el SDD nativo (sdd-explore, sdd-propose, sdd-spec...)
- **Para la verificación final:** mantener `scc-verify` (o adaptar su check suite a una skill) porque valida lo que el SDD nativo no valida

**Si eliminas `scc-verify` por completo, perderías la verificación de gobernanza.**

---

## 6. Fase 5: Decisión skills — `creador-de-skills` vs `skill-creator`

### Diferencia clave (verificada en ambos SKILL.md)

| Skill | Dónde crea | Conoce subcarpetas por meta | Registra en matriz |
|-------|-----------|:---:|:---:|
| **`creador-de-skills`** (tuya) | `.agents/skills/{subcarpeta}/` según meta/alcance | ✅ Sí | ✅ Sí |
| **`skill-creator`** (gentle-ai) | `skills/{skill-name}/SKILL.md` (config de la herramienta) | ❌ No | ❌ No |

**Si usas el `skill-creator` de gentle-ai, crearía la skill en `.config/opencode/skills/` — NO en `.agents/skills/` de tu bóveda.** Perderías la organización por metas y el registro en la matriz.

### Recomendación: CONSERVAR ambas, con desambiguación de triggers

1. **Conservar `creador-de-skills`** como la autoritativa para la bóveda (conoce subcarpetas + matriz + plantilla)
2. **Conservar `skill-creator`** para skills genéricas de gentle-ai
3. **⚠️ Desambiguar triggers** para que no colisionen:
   - Tu skill: trigger `"Crear una nueva skill (SKILL.md)"`
   - La de gentle-ai: trigger `"new skills, agent instructions..."`
   - **Riesgo:** si ambos triggers se activan con "crear skill", puede ser ambiguo cuál carga

**Acción:** En tu AGENTS.md raíz / matriz, definir explícitamente: "para crear skills de la bóveda usar SIEMPRE `creador-de-skills` (formato por metas + matriz)". Así el orquestador sabe cuál invocar.

---

## 7. Fase 6: Verificación post-migración

Checklist final:

- [ ] `gentle-ai doctor` → sin errores
- [ ] `AGENTS.md` raíz sigue intacto (comparar con backup)
- [ ] `.opencode/agents/*.md` siguen presentes
- [ ] `.agents/skills/*` siguen presentes y detectadas
- [ ] `opencode.json` raíz conserva MCP (gemini-image-gen, higgsfield, playwright)
- [ ] `gentle-orchestrator` puede llamar a `scc-*` y `gestor-*` (permisos)
- [ ] Abrir OpenCode → agente activo es `gentle-orchestrator`
- [ ] Probar una sesión: carga meta → alcance → personalidad (desde AGENTS.md raíz)
- [ ] Engram sigue funcionando (base global)
- [ ] Crear una skill de prueba con `creador-de-skills` → cae en `.agents/skills/` correcta

---

## 8. Rollback (si algo falla)

```powershell
# Restaurar desde backup
$dest = "C:\Users\alela\obsidian\backup-migracion-YYYY-MM-DD"
Copy-Item "$dest\AGENTS.md" "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\" -Force
Copy-Item -Recurse "$dest\.opencode" "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\" -Force
Copy-Item -Recurse "$dest\.agents" "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\" -Force
```

Y si gentle-ai creó `.config/opencode/` en la bóveda, se puede eliminar:

```powershell
Remove-Item -Recurse -Force "C:\Users\alela\obsidian\ALEJANDRO J LOPEZ\.config\opencode"
```

---

## 9. Riesgos y mitigaciones

| Riesgo | Probabilidad | Mitigación |
|--------|:---:|-----------|
| `install` sobrescribe `opencode.json` raíz | Media | Backup + verificar MCP tras instalar |
| Se pierde la verificación cualitativa si se borra `scc-verify` | Alta | Mantener `scc-verify` como subagente |
| `skill-creator` crea skills en carpeta equivocada | Alta | Conservar `creador-de-skills` + desambiguar triggers |
| Colisión de triggers entre skills | Media | Definir regla en AGENTS.md raíz |
| `sync` pierde permisos a subagentes custom | Baja | Verificar tras cada sync (deep-merge preserva) |
| Enrutamiento meta+alcance se pierde | Media | Depende de que `gentle-orchestrator` lea AGENTS.md raíz |

---

## 10. Orden ejecutable final

```
1. [FASE 0] Backup completo
2. [FASE 1] Ajustar referencias al orquestador en AGENTS.md raíz
3. [FASE 2] gent-ai install --scope=workspace --agents opencode --persona custom
4. [VERIFICAR] opencode.json raíz + MCP intactos
5. [FASE 3] Añadir permisos scc-*/gestor-* al gentle-orchestrator
6. [FASE 4] Decidir: mantener scc-verify para verificación cualitativa
7. [FASE 5] Desambiguar triggers creador-de-skills vs skill-creator
8. [FASE 6] Checklist de verificación
9. Eliminar orquestador.md custom (solo tras verificar todo)
10. Commit + push (tú controlas desde VS Code)
```

---

## Referencias

- [[analisis-integracion-boveda]] — Análisis completo de compatibilidad
- [[sdd]] — SDD nativo de gentle-ai
- [[skills]] — Sistema de skills
- [[subagentes-custom]] — Subagentes en `.opencode/agents/*.md`
- [[persona-y-profiles]] — Persona vs orquestador
- [[ejecutar-gentle-ai]] — Instalación y scope
- [[instalacion-global-vs-local]] — Global vs workspace
- [[cli-commands]] — Comandos CLI
