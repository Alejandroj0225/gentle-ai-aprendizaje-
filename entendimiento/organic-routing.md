# Organic Routing

## ¿Qué es?

Es el mecanismo que gentle-ai usa para decidir **cómo** implementar un cambio. Elige el camino más pequeño que resuelva.

---

## Las 3 rutas

| Ruta | Cuándo | Descripción |
|------|---------|-------------|
| **Direct inline** | 1-3 archivos, cambio mecánico | Hacerlo yo mismo, sin delegar |
| **Delegated direct** | 4+ archivos para explorar, 2+ writes no triviales | Delegar a un worker bounded |
| **SDD (opcional)** | Propuesta durable, spec, design reducirían ambigüedad | Workflow de 9 fases |

---

## El árbol de decisión

```
┌─────────────────────────────────────────────┐
│  USER REQUEST                                │
│  "Agrega auth JWT al backend"                 │
└──────────────────┬──────────────────────────┘
                   ↓
         ┌─────────────────┐
         │ ¿Cuántos archivos? │
         └────────┬──────────┘
                  ↓
    ┌─────────────┼─────────────┐
    ↓             ↓             ↓
  1-3        4+           Complejo
    ↓             ↓             ↓
 Direct      Delegated      SDD
 inline        direct      (opcional)
```

---

## Regla fundamental

> **Tamaño, cantidad de archivos, o riesgo NUNCA seleccionan SDD por sí solos.**
> Solo una solicitud explícita o una propuesta aceptada activa SDD.

---

## Ejemplos prácticos

| Situación | Ruta | Por qué |
|-----------|------|---------|
| "Corrige typo en README" | **Direct inline** | 1 archivo, cambio mecánico |
| "Agrega tests a 5 archivos" | **Delegated direct** | 4+ archivos, necesita explorar |
| "Rebuild completo del auth" | **SDD** | Cambio sustancial, requiere planificación |
| "Refactoriza 3 funciones" | **Delegated direct** | Múltiples archivos, lógica compleja |

---

## Cómo decide el agente

El agenteevalúa:

1. **Archivos afectados** — ¿1-3 o 4+?
2. **Complejidad** — ¿Es mecánico o requiere diseño?
3. **ambigüedad** — ¿Un spec reduciría confusión?

---

## Resumen visual

```
Organic Routing = "Elegir el camino más pequeño"

┌─────────────────────────────────────────────┐
│  DIRECT INLINE                                │
│  → Cambio simple                            │
│  → Yo lo hago                               │
│  → Sin delegar                              │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│  DELEGATED DIRECT                            │
│  → Cambio medio                            │
│  → Delego a un worker bounded               │
│  → Más contexto para el worker              │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│  SDD (OPCIONAL)                              │
│  → Cambio complejo                          │
│  → Planifico con specs y design            │
│  → 9 fases                                  │
└─────────────────────────────────────────────┘
```

---

## Implementación routing

| Situación | Expected behavior |
| --- | --- |
| 1-3 files, one mechanical change | Keep direct inline |
| 4+ files, broad research, 2+ non-trivial writes | Delegate without SDD |
| Durable proposal, spec, design, tasks would reduce ambiguity | Offer SDD (opt-in) |

---

## Importante

**El tamaño NO selecciona SDD.**

| Mito | Realidad |
|------|---------|
| "Es muy grande, necesito SDD" | Solo si hay ambigüedad sustancial |
| "Son muchos archivos" | Puede ser delegated direct |
| "Es risky" | No es criterio para SDD |

---

## Referencias

- [[sdd]] — Spec-Driven Development
- [[rdd]] — Receipt-Driven Development
- [[01-arquitectura-general]] — Visión general de la arquitectura
