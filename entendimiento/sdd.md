# Spec-Driven Development (SDD)

## ¿Qué es?

**SDD** (Spec-Driven Development) es el workflow de **planificación antes de codificar** de gentle-ai. Cambios sustanciales se descomponen en 9 fases que producen artifacts duraderos (proposal, spec, design, tasks) antes de implementar.

---

## Las 9 fases

```
explore → propose → spec → design → tasks → apply → verify → archive
                                   (sdd-init = bootstrap, sdd-onboard = onboarding)
```

| Fase | Qué produce | Sub-agente |
|------|-------------|-----------|
| `sdd-init` | Contexto del proyecto (stack, testing) | sdd-init |
| `sdd-explore` | Investigación del codebase e ideas | sdd-explore |
| `sdd-propose` | Propuesta: intención, scope, approach | sdd-propose |
| `sdd-spec` | Requisitos + escenarios | sdd-spec |
| `sdd-design` | Decisiones de arquitectura | sdd-design |
| `sdd-tasks` | Checklist ordenado de deliverables | sdd-tasks |
| `sdd-apply` | Implementación contra specs (TDD) | sdd-apply |
| `sdd-verify` | Verificación independiente contra spec | sdd-verify |
| `sdd-archive` | Merge de delta-specs, cierra ciclo | sdd-archive |

---

## El rol del orquestador

`gentle-orchestrator` es el **coordinador**, no el ejecutor:

- Mantiene UN hilo de conversación delgado
- Delega TODO el trabajo real a sub-agentes
- Sintetiza resultados

```
Usuario → gentle-orchestrator (coordina)
              ├──→ sdd-explore (investiga)
              ├──→ sdd-propose (propone)
              ├──→ sdd-spec (especifica)
              └──→ ... (cada fase en contexto aislado)
```

---

## ¿Cuándo se usa SDD?

**Solo cuando reduce ambigüedad sustancial** — nunca por tamaño o cantidad de archivos:

| Situación | ¿SDD? |
|-----------|:---:|
| Cambio mecánico de 1-3 archivos | ❌ Direct inline |
| 4+ archivos, exploración necesaria | ❌ Delegated direct |
| Propuesta durable + spec reducirían ambigüedad | ✅ SDD (opt-in) |
| El usuario pide explícitamente SDD | ✅ SDD |

> Regla: Solo una solicitud explícita o una propuesta aceptada activa SDD.

---

## Persistencia de artifacts

| Modo | Dónde viven los artifacts |
|------|--------------------------|
| `engram` | Solo en Engram (memoria) |
| `openspec` | Solo en archivos `openspec/` |
| `hybrid` | Ambos (Engram + filesystem) |

```
openspec/
├── config.yaml
├── specs/<feature>/     ← artifacts activos
│   ├── proposal.md
│   ├── spec.md
│   ├── design.md
│   └── tasks.md
└── changes/archive/     ← cambios completados
```

---

## RDD entra al final

Antes de `sdd-verify` y `sdd-archive`, el candidato pasa por **RDD** (si está habilitado):

```
apply → [RDD sobre el candidato congelado] → verify → archive
```

---

## Comandos / Slash commands

```bash
/sdd-new     # Inicia un cambio nuevo (explore → propose)
/sdd-explore
/sdd-propose
/sdd-spec
/sdd-design
/sdd-tasks
/sdd-apply
/sdd-verify
/sdd-archive
/sdd-status
/sdd-continue
/sdd-ff      # Fast-forward planning
/sdd-init
```

---

## Resumen

```
SDD = Planificar antes de codificar
├── 9+ fases con artifacts duraderos
├── Orquestador coordina, sub-agentes ejecutan
├── Solo cuando reduce ambigüedad sustancial
├── Persistencia: engram / openspec / hybrid
└── RDD revisa el candidato antes de verify/archive
```

---

## Referencias

- [[sdd-init]] — Inicialización del contexto SDD
- [[tests-tdd-sdd]] — Cómo los tests se generan y validan
- [[openspec]] — Sistema de archivos para SDD
- [[rdd]] — Receipt-Driven Development
- [[organic-routing]] — Cómo se elige la ruta
- [[commands-vs-skills]] — Commands y skills del flujo
