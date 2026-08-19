# Ciclo de Vida de un Proyecto con Gentle-AI — Del Momento Cero al Mantenimiento

> Guía del proceso completo de constitución y evolución de un proyecto con Gentle-AI. Sirve como **patrón de comparación** para verificar si el agente orquestador está haciendo las cosas correctamente en cada fase (ej. el proyecto `Plugin obsidian`).

---

## Fase 0 — Instalación y constitución del proyecto

Lo que hace `gentle-ai install` (scope local/workspace) al arrancar un proyecto:

```
Proyecto/
├── .git/                       ← git init + .gitignore + README (base del repo)
├── .config/opencode/           ← instalación de Gentle-AI (la "caja negra" del framework)
│   ├── AGENTS.md               ← persona (Senior Architect) + protocolo Engram
│   ├── opencode.json           ← define agentes: gentle-orchestrator (primario) + subagentes
│   ├── commands/               ← sdd-new, sdd-init, sdd-status, skill-creator, skill-registry...
│   ├── skills/                 ← todas las skills SDD (sdd-*, rdd, skill-creator, _shared...)
│   ├── plugins/                ← model-variants, opencode-review-transport, skill-registry...
│   └── .gentle-ai-default-agent.json
└── (después) AGENTS.md          ← AGENTS.md de proyecto en la RAÍZ (contexto propio del proyecto)
```

**Puntos a verificar en esta fase:**
- [ ] ¿Existe `.config/opencode/` con `opencode.json`, `AGENTS.md`, `commands/`, `skills/`, `plugins/`?
- [ ] ¿`opencode.json` define a `gentle-orchestrator` como agente primario (`mode: primary`) y `default_agent`?
- [ ] ¿Los subagentes `sdd-*` y `review-*` están declarados y con permiso de invocación desde el orquestador?
- [ ] ¿MCP está configurado (engram + context7)? (`mcp.engram` y `mcp.context7`)
- [ ] ¿Repo git inicializado (`.git`, `.gitignore`, `README`)?

---

## Fase 1 — Primer arranque y onboarding

Cuando abrís OpenCode por primera vez en el proyecto:

```
1. Abres OpenCode → el plugin refresca el skill registry automáticamente
2. (Si no hay contexto SDD) el orquestador corre /sdd-init
   └── detecta stack real (package.json, go.mod, pyproject.toml...)
       detecta test runner / testing capabilities
       resuelve Strict TDD (marcador/config → runner detectado → sin runner = false)
       crea .atl/skill-registry.md
       persiste en Engram: sdd-init/{project}, sdd/{project}/testing-capabilities, skill-registry
3. Engram inyecta contexto de sesiones previas (mem_context)
4. (Opcional) /sdd-onboard = primer ciclo SDD guiado paso a paso con un cambio real pequeño
```

**Conceptos clave:**
- **SDD Session Preflight** (antes de `/sdd-new`): define execution mode, artifact store (engram | openspec | hybrid | none), chained PR strategy y review budget. Si falta, el orquestador DEBE pedirlo con la pregunta exacta y PARAR.
- **Artifact store**: dónde persisten los artefactos SDD (Engram y/o openspec/).
- **Strict TDD**: si hay test runner → `strict_tdd: true` (apply trabaja test-first: RED → GREEN → REFACTOR). Sin runner → false.

**Puntos a verificar:**
- [ ] ¿Se corrió `sdd-init` y creó `.atl/skill-registry.md`?
- [ ] ¿Se detectó el stack real y las testing capabilities (no adivinó)?
- [ ] ¿Las observaciones quedaron en Engram (`sdd-init/{project}`, `testing-capabilities`, `skill-registry`)?
- [ ] ¿Antes de cualquier `/sdd-new` se hizo el preflight (execution mode, artifact store, PR strategy, review budget)?

---

## Fase 2 — Definir el contexto del proyecto

Lo que el proyecto debe tener para que el orquestador trabaje bien:

1. **`AGENTS.md` en la raíz** del proyecto — contexto, reglas, convenciones, stack acordado, referencias externas (rutas a bóveda, plugin de referencia, repo de aprendizaje).
2. **Stack definido** — acordado con el usuario (ej. Node.js + TypeScript + esbuild + obsidian-api para plugins de Obsidian).
3. **(Si mode=openspec/hybrid)** `openspec/config.yaml` — contexto breve, strict_tdd, testing capabilities, reglas de fases.

> ⚠️ **Diferencia clave**: el `AGENTS.md` en `.config/opencode/` es la **configuración del framework** (persona + protocolo); el `AGENTS.md` en la raíz es el **contexto del proyecto** (qué es, cómo se construye, reglas propias). Ambos coexisten.

**Puntos a verificar:**
- [ ] ¿Existe AGENTS.md en la raíz con contexto del proyecto (no solo el de `.config/opencode/`)?
- [ ] ¿El stack fue definido con el usuario (con explicación de cada elección)?
- [ ] ¿Las decisiones de diseño se guardaron en Engram (mem_save) y/o openspec?

---

## Fase 3 — Desarrollo de features (el ciclo SDD por cambio)

El flujo típico para cada feature nueva:

```
/sdd-new "descripción del cambio"
  1. sdd-explore  (subagente) → investiga el código, presenta hallazgos
  2. sdd-propose  (subagente) → crea proposal.md (el QUÉ y el PORQUÉ)
  3. Presenta el proposal al usuario → pregunta si continúa (GATE de aprobación)
  4. sdd-spec     (subagente) → spec.md (requisitos + escenarios Given/When/Then)
  5. sdd-design   (subagente) → design.md (el CÓMO, decisiones y rationale)
  6. sdd-tasks    (subagente) → tasks.md (pasos concretos y verificables)
  7. sdd-apply    (subagente) → implementa (test-first si strict TDD)
  8. sdd-verify   (subagente) → matriz de cumplimiento (COMPLIANT/FAILING/UNTESTED)
  9. sdd-archive  (subagente) → mergea specs → closes el cambio
```

**Reglas de oro del orquestador:**
- Es un **COORDINADOR, no ejecutor**: delega TODO el trabajo real a subagentes (`sdd-*`), él sintetiza. Si hace el trabajo inline en vez de delegar, es una señal de mal comportamiento.
- **HARD GATE** en `/sdd-new`: el preflight debe estar completo ANTES de lanzar exploración. No lanza explore+propose en el mismo turno sin preflight.
- Los subagentes persisten automáticamente en el artifact store con topic_key `sdd/{cambio}/{tipo}`.
- Nunca hace más de una pregunta a la vez; pide confirmación en los gates (proposal, spec).

**Puntos a verificar:**
- [ ] ¿Usó subagentes `sdd-*` (delegó) en vez de ejecutar inline?
- [ ] ¿Pidió aprobación del proposal antes de continuar con spec/design?
- [ ] ¿Cada fase persiste en el artifact store (Engram u openspec)?
- [ ] ¿Verify devuelve matriz de cumplimiento contra los escenarios del spec?
- [ ] ¿El cambio quedó archivado (archive) y los specs actualizados?

---

## Fase 4 — Mantenimiento y expansión (proyecto maduro)

Cuando el proyecto ya está constituido, el día a día es:

```
├── Abres OpenCode → contexto listo (skill registry + Engram)
├── Cambios pequeños (1-3 archivos, sin ambigüedad) → DIRECT INLINE
├── Cambios medianos (4+ archivos) → DELEGATED DIRECT
├── Cambios sustanciales / ambigüedad → SDD completo (/sdd-new)
├── (Si RDD activo) revisión bounded + receipt en el delivery gate
├── Commit → tú controlas el push (VS Code)
└── Cierre → mem_session_summary
```

**Comandos de mantenimiento:**
- `gentle-ai doctor` — ¿todo bien? diagnóstico
- `gentle-ai sync` — refrescar assets (skills, comandos)
- `gentle-ai upgrade` — actualizar framework
- `gentle-ai skill-registry refresh` — reconstruir registry
- `/sdd-status` — estado de cambios en curso

**Regla de ruteo (organic routing):** siempre la ruta más pequeña que resuelva el cambio. SDD solo cuando reduce ambigüedad sustancial. No usar SDD para todo.

**Puntos a verificar:**
- [ ] ¿Elige la ruta más pequeña (direct → delegated → SDD)? (si usa SDD para todo, es señal de exceso)
- [ ] ¿Las decisiones importantes se guardan en Engram?
- [ ] ¿Cierra la sesión con `mem_session_summary`?
- [ ] ¿Los cambios sustanciales quedan con spec/design como fuente de verdad creciente?

---

## Checklist rápida "¿está bien el comportamiento del orquestador?"

| Momento             | Debería hacer                                          | Señal de alerta                               |
| ------------------- | ------------------------------------------------------ | --------------------------------------------- |
| Instalación         | Crear `.config/opencode/` completo + git               | Falta opencode.json o subagentes              |
| Primer arranque     | `sdd-init` + skill-registry + Engram                   | No detecta stack, adivina testing             |
| Antes de `/sdd-new` | Preflight completo (mode, store, PR, budget)           | Lanza explore sin preflight                   |
| Durante SDD         | Delegar a subagentes `sdd-*`, preguntar en gates       | Ejecuta inline, no pide aprobación            |
| Feature             | Spec → apply test-first → verify → archive             | Código sin spec ni verify                     |
| Día a día           | Ruta más pequeña (direct/delegated/SDD)                | SDD para todo, o directo para cambios grandes |
| Memoria             | mem_save de decisiones + mem_session_summary al cerrar | No persiste nada                              |

---

## Referencias

- [[sdd]] — Workflow de planificación de 9 fases
- [[sdd-init]] — Inicialización del contexto SDD
- [[organic-routing]] — Elegir la ruta de implementación
- [[flujo-trabajo]] — Sesión diaria típica
- [[instalacion-global-vs-local]] — Global vs local
- [[commands-vs-skills]] — Comandos y skills
- [[engram]] — Memoria persistente
- [[agents-md-config-vs-raiz]] — AGENTS.md de config vs raíz

---

*Nota creada: 2026-08-19*
