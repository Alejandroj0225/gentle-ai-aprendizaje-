# AGENTS.md Anidados - Soporte en Gentle-AI

## Pregunta Original

> ¿Soporta Gentle-AI la posibilidad de crear agents.md anidados por componentes (frontend, backend, etc.)?

## Respuesta Corta

**No hay soporte nativo** en Gentle-AI para la mecánica de `agents.md` anidados en subdirectorios. Esta es más una práctica de Cursor con `.cursorrules` o Claude Code con `CLAUDE.md` en subcarpetas.

## Lo que Gentle-AI SÍ hace

### 1. Escaneo de Convention Files

Gentle-AI al iniciar (`sdd-init`) escanea estos archivos de convención en la **raíz del proyecto**:

```
agents.md, AGENTS.md, project-level CLAUDE.md, .cursorrules, GEMINI.md, copilot-instructions.md
```

**No busca en subdirectorios.**

### 2. AGENTS.md como Índice

Cuando encuentra un `AGENTS.md`, Gentle-AI puede extraer rutas referenciadas dentro de él:

```markdown
<!-- Si AGENTS.md incluye: -->
@include: frontend/agents.md
@include: backend/agents.md
```

Pero esto es una **referencia manual** dentro del archivo, no estructura de subdirectorios automática.

### 3. Skill Registry

El skill registry escanea skills a **un nivel de profundidad**:

```
{project-root}/skills/           ← OK
{project-root}/skills/skill/     ← NO (profundidad > 1)
```

## Convención de Agentes por Componente

| Agente | Archivo de Contexto | Soporte Anidado |
|--------|--------------------|--------------------|
| Cursor | `.cursorrules` | ✅ Sí, en subcarpetas |
| Claude Code | `CLAUDE.md` | ✅ Sí, en subcarpetas |
| VS Code Copilot | `copilot-instructions.md` | ❌ No |
| OpenCode | `AGENTS.md` | ❌ No |

Gentle-AI usa la convención de OpenCode, que **no soporta anidamiento** de `AGENTS.md`.

## Alternativas en Gentle-AI

Si necesitas contexto específico por componente, puedes:

### Opción 1: Usar OpenSpec
```
openspec/
├── config.yaml              ← Configuración general
├── specs/
│   └── frontend/
│       └── config.yaml      ← Config por spec
```

### Opción 2: Múltiples Proyectos
Cada componente como proyecto separado con su propio `gentle-ai install --scope=workspace`.

### Opción 3: Engram Memory
Usar la memoria de Engram para guardar contexto específico por componente y recuperarlo cuando sea relevante.

## Conclusión

Gentle-AI no soporta `agents.md` anidados porque:
1. OpenCode (el agente que usas) no tiene ese mecanismo
2. El skill registry intentionally escanea solo un nivel
3. Los convention files se escanean solo en la raíz del proyecto

La convención de agents.md anidados es principalmente de **Cursor** y **Claude Code**, no de OpenCode.

---

## Referencias

- `sdd-init` skill: `init-details.md` línea 18
- Skill registry: `registry.go` - scan de un solo nivel
