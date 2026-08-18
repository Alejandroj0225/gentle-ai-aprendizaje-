# Receipt-Driven Development (RDD)

## ¿Qué es?

**RDD** (Receipt-Driven Development) es el sistema de **revisión bounded** de gentle-ai. Un candidato de cambio se congela, se revisa con lentes de evidencia, y se emite un **receipt** (recibo) que cada delivery gate (commit, push, PR) valida.

> "Confía en lo que el sistema puede derivar, no en la narración del agente."

---

## Es opt-in

```bash
# Ver estado
gentle-ai review mode status --cwd .

# Habilitar (solo forma de activarlo)
gentle-ai review mode enable --scope global --cwd .

# Deshabilitar
gentle-ai review mode disable --cwd .
```

**RDD está OFF hasta que lo habilites explícitamente.** Con review off, la entrega sigue la política ordinaria del repositorio y reporta `disabled/unmanaged`.

---

## Flujo de revisión

```
Candidato congelado (bytes exactos)
        ↓
Riesgo evaluado al START
        ↓
┌─────────────────────────────────────────────────────────────┐
│  Riesgo LOW      → Structural readback (0 lentes, silente) │
│  Riesgo STANDARD → 1 focus lens + consent                   │
│  Riesgo HIGH     → Canonical 4R + consent + forecast       │
└─────────────────────────────────────────────────────────────┘
        ↓
Reviewers inspeccionan el candidato inmutable
        ↓
¿Findings severos causados por el candidato?
├── NO → Receipt: approved
└── SÍ → Una corrección bounded (presupuesto congelado)
         → Fix validator (read-only, árboles inmutables)
         ├── Pasa → Receipt: approved
         ├── Falla con evidencia → Escalated → review recover
         └── Sin acceso al diff → Inconclusive (re-capture)
```

---

## Los 4R (Canonical Review)

| Lente              | Qué revisa                                                    |
| ------------------ | ------------------------------------------------------------- |
| **R1 Risk**        | Seguridad, privilegios, exposición de datos, vulnerabilidades |
| **R2 Readability** | Naming, complejidad, intención, mantenibilidad                |
| **R3 Reliability** | Tests, edge cases, determinismo, contratos                    |
| **R4 Resilience**  | Fallbacks, retry, degradación, observabilidad, rollback       |

---

## Receipts

El receipt es la **evidencia exacta** de la revisión:

- Se emite UNO solo por candidato
- Commit, push y PR **validan el mismo receipt**
- La revisión nunca se reabre por contenido sin cambios

---

## Delivery Gates

```
Pre-commit → Pre-push → Pre-PR
     ↓           ↓         ↓
Validan el MISMO receipt (content-bound)
```

---

## Corrección bounded

Si hay findings severos:

1. Una sola corrección con **presupuesto congelado**
2. Fix validator (read-only) valida la corrección
3. Si pasa → receipt approved
4. Si falla con evidencia → **Escalated** → `gentle-ai review recover` (sucesor autorizado)

---

## Comandos relacionados

```bash
gentle-ai review mode status
gentle-ai review mode enable --scope global
gentle-ai review mode disable
gentle-ai review start --projection staged
gentle-ai review inspect-candidate
```

---

## Resumen

```
RDD = Revisión bounded con evidencia
├── Candidato congelado
├── Riesgo → lentes (0, 1, o 4R)
├── Un receipt por candidato
├── Corrección bounded (1 sola, presupuesto congelado)
└── Delivery gates validan el mismo receipt
```

---

## Referencias

- [[organic-routing]] — Cómo se elige la ruta de implementación
- [[sdd]] — Spec-Driven Development (verify requiere receipt)
- [[01-arquitectura-general]] — Visión general
