# Ejecutar Gentle-AI — TUI, Scope y Binario

## Introducción

Tres conceptos que se confunden fácilmente: **cómo se ejecuta** `gentle-ai`, **dónde escribe** la configuración, y **qué es global vs local**.

---

## 1. Binario vs Configuración (global/local)

| Componente | ¿Local o Global? |
|-----------|-----------------|
| **El binario `gentle-ai`** | Siempre global (en PATH, ej: `C:\Users\<user>\go\bin\gentle-ai.exe`) |
| **La configuración que escribe** | Depende del `--scope` que elijas |

El binario es **un solo ejecutable** global. Lo que varía es **dónde escribe la configuración** de los agentes.

---

## 2. El scope decide dónde se escribe

```
El scope NO se deduce de la carpeta donde ejecutas.
Se decide por: flag --scope, pantalla del TUI, o el default.
```

| Ejecutas... | Resultado |
|------------|-----------|
| `gentle-ai install --agents opencode` (sin scope) | **GLOBAL** → `~/.config/opencode/` |
| `gentle-ai install --agents opencode --scope=workspace` | **LOCAL** → `.config/opencode/` de esa carpeta |

**Por defecto es GLOBAL.** Estar parado en una carpeta no la convierte en el destino.

### Ejemplo concreto

```
Crear carpeta: C:\prueba-de-proyecto
Pararte ahí y ejecutar:
```

```bash
# Caso A: Sin scope → GLOBAL (escribe en C:\Users\alela\.config\opencode\)
gentle-ai install --agents opencode

# Caso B: Con workspace → LOCAL (escribe en C:\prueba-de-proyecto\.config\opencode\)
gentle-ai install --agents opencode --scope=workspace
```

### Env var (para CI / no interactivo)

```powershell
$env:GENTLE_AI_INSTALL_SCOPE="workspace"
gentle-ai install --agents opencode
```

### ¿Y el estado guardado?

`~/.gentle-ai/state.json` guarda tu selección previa (agents + scope). `sync` y el TUI usan esa selección — es el estado guardado, no la carpeta.

---

## 3. Hay cosas global-only por diseño

| Componente | ¿Local? | Por qué |
|-----------|---------|---------|
| Skills, prompts, SDD agents, persona | ✅ Local (si scope=workspace) | Son "agent-scoped files" |
| **Plugins de comunidad de OpenCode** | ⚠️ Global | Se registran en `~/.config/opencode/tui.json` |
| `state.json` | ⚠️ Global | Siempre vive en `~/.gentle-ai/` |
| Package installs | ⚠️ Global | Instalaciones de paquetes |

> "Global-only integrations, such as package installs or settings that the agent only reads from its global config, remain global by design."

---

## 4. ¿Para qué ejecutar `gentle-ai` sin argumentos?

Abre el **TUI interactivo** — la "puerta de entrada visual" a la herramienta.

| CLI (flags) | TUI (gentle-ai solo) |
|------------|----------------------|
| `gentle-ai install --agents opencode` | Menús con flechas y Enter |
| `gentle-ai sync --profile cheap:...` | Menú "OpenCode SDD Profiles" → Create |
| Recuerdas cada flag | Todo visual, sin memorizar flags |

### Menús del TUI

```
Gentle-AI
├── Install (instalar/configurar agentes)
├── Community Tools
├── Plugins manage
├── Installed reset
├── Review store
├── Manage backups
├── OpenCode SDD Profiles  ← crear perfiles de modelos
├── OpenCode plugins
├── OpenCode community plugins
├── Configure Models       ← asignar modelos a fases
└── Uninstall flows
```

### ¿Importa la carpeta donde estás parado?

- **Para la mayoría de operaciones** (install, profiles, plugins): la carpeta NO decide nada — el scope lo eliges dentro del TUI (o default global)
- **Para otras** (doctor, skill-registry): importa la carpeta porque escanea el proyecto actual

### Analogía

```
gentle-ai          = El "escritorio" con todos los menús
gentle-ai install  = Un atajo directo a una pantalla específica
```

### ¿Cuándo usar cada uno?

| Situación | Usa |
|-----------|-----|
| No recuerdas los flags | `gentle-ai` (TUI) |
| Quieres ver todas las opciones | `gentle-ai` (TUI) |
| Sabes exactamente qué hacer | CLI con flags |
| Automatización / scripts | CLI con flags |

---

## Resumen

```
Binario    → SIEMPRE global (PATH)
Scope      → LO DECIDES tú (default: global)
Carpeta    → NO decide el scope
Global-only → plugins de comunidad, state.json, binario
TUI        → gentle-ai solo = menús interactivos
CLI        → flags directos
```

---

## Referencias

- [[cli-commands]] — Comandos CLI de gentle-ai
- [[instalacion-global-vs-local]] — Global vs local en detalle
- [[persona-y-profiles]] — Perfiles SDD (OpenCode SDD Profiles)
