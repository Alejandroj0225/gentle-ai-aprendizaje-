# sdd-init — Inicialización del Contexto SDD

## ¿Qué es?

**SDD Init** es el comando (`/sdd-init`) que inicializa el contexto de Spec-Driven Development en un proyecto. Es el "primer paso" para que el ecosistema conozca el proyecto sobre el que va a trabajar.

---

## ¿Cada cuánto se ejecuta?

| Momento | ¿Hacerlo? |
|---------|-----------|
| **Primera vez en un proyecto nuevo** | ✅ Sí |
| **Cuando agregas/quitas frameworks de testing** | ✅ Re-ejecutar |
| **Al cambiar de stack** | ✅ Re-ejecutar |
| **En cada sesión** | ❌ No — el orquestador lo corre automático si detecta que falta contexto |

> El orquestador de SDD ejecuta `/sdd-init` automáticamente si detecta que no hay contexto previo.

---

## ¿Qué detecta?

```
sdd-init escanea el proyecto
        ↓
┌─────────────────────────────────────────────────────────────┐
│ 1. STACK: ¿Qué tecnologías usa el proyecto?                │
│    package.json → Node.js + React + TypeScript?            │
│    go.mod       → Go?                                      │
│    pyproject.toml → Python?                                │
│    Cargo.toml  → Rust?                                     │
└─────────────────────────────────────────────────────────────┘
        ↓
┌─────────────────────────────────────────────────────────────┐
│ 2. TESTING: ¿Qué herramientas de test hay?                 │
│    Scripts en package.json → vitest / jest?                │
│    pytest.ini → pytest?                                    │
│    go.mod → go test?                                       │
│    Makefile → comandos custom?                             │
│    playwright/cypress → E2E?                               │
└─────────────────────────────────────────────────────────────┘
        ↓
┌─────────────────────────────────────────────────────────────┐
│ 3. SKILLS: ¿Qué skills hay disponibles?                    │
│    Escanea ~/.config/opencode/skills/ (user)               │
│    Escanea {proyecto}/skills/ (project)                    │
│    Escanea .opencode/skills/, .claude/skills/, etc.        │
│    Construye el skill registry (.atl/skill-registry.md)    │
└─────────────────────────────────────────────────────────────┘
        ↓
┌─────────────────────────────────────────────────────────────┐
│ 4. CONVENCIONES: ¿Qué archivos de reglas hay?              │
│    AGENTS.md, agents.md, CLAUDE.md, .cursorrules,          │
│    GEMINI.md, copilot-instructions.md                      │
└─────────────────────────────────────────────────────────────┘
```

---

## ¿Cómo detecta el stack y el testing?

**No adivina** — **lee archivos reales del proyecto** que ya existen porque tú trabajas con esa tecnología:

| Archivo que busca | Qué le dice | Quién lo creó |
|-------------------|-------------|---------------|
| `package.json` | Node.js, frameworks, test runner, coverage | npm / scaffold (vite, next) |
| `go.mod` | Go, versión | `go mod init` |
| `pyproject.toml` | Python, pytest | poetry / uv / pip |
| `Cargo.toml` | Rust | `cargo new` |
| `Makefile` | Comandos build/test custom | Tú, a mano |
| `pytest.ini` | Config de pytest | Tú o herramientas |

---

## ⚠️ Punto clave: sdd-init NO crea esos archivos

**sdd-init solo los LEE.** Los archivos de stack existen porque tú scaffoldesate el proyecto con tu tecnología:

```
1. TÚ creas un proyecto con tu tecnología
   (npm create vite, go mod init, cargo new...)
   → Se generan package.json, go.mod, etc. AUTOMÁTICAMENTE
        ↓
2. TÚ abres OpenCode en ese proyecto
        ↓
3. sdd-init LEE esos archivos (no los crea)
   → Detecta stack, testing, skills, convenciones
        ↓
4. Guarda el contexto (Engram / openspec/config.yaml)
```

---

## ¿Cómo se crean los archivos de stack?

| Tecnología | Comando | Archivo generado |
|------------|---------|------------------|
| React/Vue + TS | `npm create vite@latest mi-app` | `package.json` (solo) |
| Next.js | `create-next-app` | `package.json` (solo) |
| Go | `go mod init mi-servicio` | `go.mod` |
| Rust | `cargo new mi-herramienta` | `Cargo.toml` (solo) |
| Python | `poetry init` / `uv init` | `pyproject.toml` |
| Custom builds | — | `Makefile` (a mano) |

---

## ¿Qué pasa si el proyecto NO tiene esos archivos?

Si ejecutas `/sdd-init` en un proyecto vacío:

- No puede detectar stack
- No puede detectar testing
- Reporta "no stack detected"
- NO activa Strict TDD Mode

**Flujo correcto:**
```
1. Primero creas el proyecto (npm create, go mod init...)
2. Después ejecutas /sdd-init
```

---

## Flujo interno completo

```
1. Ejecutas /sdd-init
2. El orquestador delega a sub-agente sdd-init
3. Sub-agente escanea el filesystem:
   - Lee package.json / go.mod / pyproject.toml
   - Busca frameworks de testing
   - Escanea carpetas de skills
   - Busca convention files
4. Decide modo de persistencia (engram / openspec / hybrid)
5. Guarda contexto:
   - Engram: mem_save (contexto del proyecto)
   - OpenSpec: crea openspec/ con config.yaml
6. Crea .atl/skill-registry.md
7. Reporta el resumen al orquestador
```

---

## ¿Qué guarda?

Ejemplo en `openspec/config.yaml`:

```yaml
stack: typescript-react
strict_tdd: true
rules:
  apply:
    tdd: true
    test_command: "npm test"
```

Y en Engram (mem_save):
- `sdd-init/{project}` → contexto del proyecto
- `sdd/{project}/testing-capabilities` → capacidades de testing
- `skill-registry` → registry de skills

---

## ¿Tiene sentido sdd-init en un proyecto de estudio? (Ejemplo: este proyecto)

### Respuesta corta: NO

Este proyecto es de **estudio/documentación**, no de desarrollo:

| Criterio | Este proyecto | Proyecto de desarrollo real |
|----------|:---:|:---:|
| ¿Vas a crear features? | ❌ No | ✅ Sí |
| ¿Hay stack de desarrollo? | ⚠️ Go (del repo clonado, no tuyo) | ✅ Sí |
| ¿Vas a escribir tests? | ❌ No | ✅ Sí |
| ¿Necesitas specs/designs? | ❌ No | ✅ Sí |

sdd-init prepara el terreno para **desarrollar features con SDD** — no es el objetivo de un proyecto de aprendizaje.

---

### Pero, ¿qué pasaría si se ejecuta aquí?

| Archivo | ¿Se crearía? | Nota |
|---------|:---:|------|
| `openspec/config.yaml` | ⚠️ Ya existe (viene con el repo) | Preguntaría antes de tocarlo |
| `.atl/skill-registry.md` | ✅ Sí, nuevo | Registry de skills |
| Engram saves | ✅ Sí | Contexto, testing, registry |

**Regla del skill:** "Si `openspec/` ya existe, reporta lo que existe y PREGUNTA antes de actualizarlo."

**Riesgo:** Mínimo — no crearía nada destructivo, solo preguntaría.

---

## Resumen

```
¿Qué es?       → Inicialización del contexto SDD del proyecto
¿Cuándo?       → Primera vez + cuando cambia el stack/testing
¿Cómo detecta? → Leyendo archivos reales (package.json, go.mod...)
¿Crea archivos?→ NO — solo los LEE
¿Qué guarda?   → Stack, testing, skills, convenciones
¿Cada cuánto?  → Una vez por proyecto (o automático)
¿Proyecto de estudio? → NO tiene sentido ejecutarlo
```

---

## Referencias

- [[tests-tdd-sdd]] — Cómo los tests se generan y ejecutan
- [[openspec]] — Donde se guarda el contexto detectado
- [[organic-routing]] — Cómo se elige la ruta de implementación
- [[sdd]] — Spec-Driven Development (por crear)
