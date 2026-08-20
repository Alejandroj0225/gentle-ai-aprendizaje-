# Resolución de Config en OpenCode — rutas y disable de agentes globales

## Resumen

Hallazgo empírico (verificado con OpenCode 1.18.18) sobre **cómo OpenCode resuelve la configuración** y por qué un agente definido en la config **global** aparece en **todos** los proyectos, incluso en los que parecen tener su propia instalación local.

Dos conceptos clave:
1. **Qué rutas de config de proyecto lee OpenCode** — y cuál NO.
2. **Cómo ocultar un agente global en un solo proyecto** sin tocar la config global.

---

## 1. Qué rutas lee OpenCode como config de proyecto

OpenCode hace merge de la config **global** (`~/.config/opencode/opencode.json`) con la del **proyecto**. Pero la config de proyecto SOLO se lee desde:

| Ruta dentro del proyecto | ¿La lee OpenCode? |
|--------------------------|-------------------|
| `opencode.json` (raíz) | ✅ Sí |
| `.opencode/opencode.json` | ✅ Sí |
| `opencode.jsonc` (raíz) | ✅ Sí |
| **`.config/opencode/opencode.json`** | ❌ **NO** |

> ⚠️ **Ojo**: `.config/opencode/opencode.json` dentro de un proyecto **NO es leída por OpenCode**. Esa carpeta es solo la raíz de la config **global** (`~/.config/opencode/`).

## 2. Consecuencia: instalación `--scope=workspace` inefectiva

`gentle-ai install --scope=workspace --agents opencode` escribe la configuración en `<proyecto>/.config/opencode/opencode.json`.

Como OpenCode **no lee** esa ruta, la instalación **workspace** para el agente OpenCode **no surte efecto** (al menos en OpenCode 1.18.18):

- El agente `gentle-orchestrator` que aparece en un proyecto sin `opencode.json` en la raíz **no viene de la instalación local** — viene del **global**.
- Los sub-agentes `sdd-*`, `jd-*`, `review-*` instalados en `.config/opencode/opencode.json` del proyecto **no se registran** en OpenCode.

### Cómo verificarlo

```bash
# Config resuelta (merge global + proyecto) — ver si cargó lo esperado
opencode debug config

# Detalle de un agente — de dónde sale su prompt
opencode debug agent gentle-orchestrator

# Lista de agentes disponibles
opencode agent list
```

Si `debug config` no muestra los agentes de tu instalación local, esa instalación no está siendo leída.

---

## 3. Ocultar un agente global en un solo proyecto

Para que un agente definido en la config **global** NO aparezca en un proyecto específico (sin tocar el global):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "gentle-orchestrator": {
      "disable": true
    }
  }
}
```

- Se agrega al config **del proyecto** que OpenCode sí lee (`opencode.json` raíz o `.opencode/opencode.json`).
- Efecto: el agente desaparece **solo** en ese proyecto. El global y los demás proyectos quedan intactos.
- Verificado: `opencode agent list` ya no muestra el agente y `opencode debug agent <nombre>` responde `Agent not found`.

---

## 4. Aplicación real (bóveda de Obsidian)

- **Problema**: `gentle-orchestrator` aparecía al ejecutar OpenCode en la bóveda `ALEJANDRO J LOPEZ`, aunque ahí nunca se instaló localmente.
- **Causa**: la bóveda no tiene instalación local; el agente venía del **global** (`~/.config/opencode/opencode.json`), que se mergea en todos los proyectos.
- **Solución**: agregar `"gentle-orchestrator": { "disable": true }` al `opencode.json` de la bóveda.
- **Resultado**: el agente ya no aparece en la bóveda; el repo y el plugin de Obsidian lo conservan vía global.

---

## 5. Qué dice el doc oficial de gentle-ai (y dónde está el desacuerdo)

El doc oficial (`docs/agents.md`, línea 30) describe la **intención** de `--scope=workspace`:

> In workspace scope, Gentle AI writes system prompts, skills, SDD agents, and persona files into the current project root **when the agent supports project-local configuration**. Global-only integrations, such as package installs or settings that the agent only reads from its global config, remain global by design.

Puntos del doc que son correctos y matizan el hallazgo:

- **Skills globales por diseño**: OpenCode auto-carga las external skills de `~/.agents/skills` (y `~/.claude/skills`) en **todos** los proyectos — es arquitectura de OpenCode, no una decisión del usuario.
- **No se filtran specs entre proyectos**: las skills globales son solo instrucciones ("cómo hacer SDD", "cómo revisar"), no datos. Los artefactos SDD (specs, diseños, receipts) se escriben **siempre dentro del repo** donde se trabaja (en `openspec/` o `.gentle-ai/` local), nunca en carpetas globales compartidas.

**El desajuste central**: el doc asume que OpenCode *supports project-local configuration*. Con OpenCode **1.18.18** eso **no se cumple** para la ruta `.config/opencode/opencode.json` (solo lee `opencode.json` raíz y `.opencode/opencode.json` — verificado dos veces, incluida una prueba dentro de un repo git: `Agent probe-ws-git not found`). Consecuencia:

- La instalación workspace de gentle-ai escribe los agentes en una ruta que OpenCode 1.18.18 **no lee**.
- El `gentle-orchestrator` que aparece en repo, Plugin obsidian y bóveda viene **siempre del global**.

**Conclusión del contraste**: una respuesta del agente del plugin citaba el doc correctamente, pero asumía que OpenCode lee la config workspace del proyecto. La verificación empírica (`opencode debug config`) es la fuente de verdad para el binario instalado: el comportamiento real difiere del supuesto del doc. Puede ser un bug del adaptador opencode de gentle-ai o un desfase con versiones más nuevas de OpenCode.

---

## Notas

- **OpenCode no recarga la config en caliente**: tras editar config, hay que cerrar y reabrir OpenCode.
- **Posible bug de gentle-ai a reportar**: el adaptador OpenCode de `--scope=workspace` escribe en `.config/opencode/opencode.json`, una ruta que OpenCode 1.18.18 no lee como config de proyecto. El doc oficial (`docs/agents.md`, línea 30) asume que OpenCode *supports project-local configuration*; con la versión 1.18.18 no es así.

---

## Referencias

- [[instalacion-global-vs-local]] — Cuándo instalar global vs local (tema padre)
- [[agents-md-config-vs-raiz]] — Diferencia AGENTS.md en `.config/opencode/` vs raíz
- [[ejecutar-gentle-ai]] — TUI vs CLI, scope (global/local) y binario
