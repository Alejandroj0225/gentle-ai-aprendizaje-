# Tests, TDD y SDD — Cómo funcionan

## Resumen

- **sdd-apply** → GENERA los tests + implementación (siguiendo TDD)
- **sdd-verify** → EJECUTA los tests y valida contra la spec
- Los tests NO vienen incluidos — el agente los escribe según el stack del proyecto

---

## TDD dentro de SDD

```
SDD (workflow completo)
└── sdd-apply
    └── TDD (Strict TDD Mode)
        ├── RED: Escribir test primero (falla)
        ├── GREEN: Implementar código mínimo (pasa)
        └── REFACTOR: Mejorar sin romper tests
```

---

## ¿Tests nativos o propios?

**NO existen tests nativos de la herramienta para tu proyecto.**

Los ~6000 tests del autor de Gentle-AI son tests del **propio proyecto gentle-ai** (escrito en Go), no tests que vengan incluidos para el usuario.

---

## ¿Quién genera los tests?

El agente (`sdd-apply`) los genera usando el framework del proyecto:

| Stack | Framework detectado |
|-------|---------------------|
| TypeScript/JS | vitest, jest |
| Python | pytest |
| Go | go test |
| Rust | cargo test |
| Java/.NET | JUnit, xUnit |

---

## ¿Cómo detecta el framework?

`sdd-init` escanea:

| Archivo | Detecta |
|---------|---------|
| `package.json` | Test runner + scripts |
| `pyproject.toml` / `pytest.ini` | pytest |
| `go.mod` | go test |
| `Cargo.toml` | cargo test |
| `Makefile` | Comandos custom |

Guarda el resultado en `openspec/config.yaml`:

```yaml
strict_tdd: true
rules:
  apply:
    tdd: true
    test_command: "npm test"
```

---

## Anatomía de un test

### TypeScript (vitest)

```typescript
import { describe, it, expect } from "vitest"
import { login } from "./auth"

describe("login", () => {
  it("retorna JWT cuando credenciales son válidas", () => {
    const token = login("user@mail.com", "password123")
    expect(token).toBeDefined()
  })
})
```

### Go

```go
func TestLogin(t *testing.T) {
    token, err := login("user@mail.com", "password123")
    if err != nil {
        t.Fatalf("expected no error, got %v", err)
    }
    if token == "" {
        t.Fatal("expected token to be non-empty")
    }
}
```

**No son markdown ni scripts custom** — son código real del lenguaje del proyecto.

---

## Flujo completo

```
┌─────────────────────────────────────────────────────────────┐
│  spec.md dice: "El login debe retornar JWT"                 │
│       ↓                                                      │
│  sdd-apply:                                                 │
│  │  RED: escribe auth.test.ts (falla)                      │
│  │  Implementa login()                                      │
│  │  GREEN: auth.test.ts pasa                                │
│  │  Refactoriza                                             │
│       ↓                                                      │
│  sdd-verify:                                                │
│  │  1. Ejecuta npm test (todos los tests)                  │
│  │  2. Verifica coverage >= mínimo                         │
│  │  3. Compara contra spec.md requisito por requisito       │
│  │  4. Reporta COMPLIANT / NON-COMPLIANT                    │
│       ↓                                                      │
│  ¿Pasa? → sdd-archive (cierra ciclo)                        │
│  ¿Falla? → vuelve a sdd-apply (corrige)                     │
└─────────────────────────────────────────────────────────────┘
```

---

## sdd-verify en detalle

Verificación **independiente**:

1. Corre el test runner (`npm test`, `go test`, etc.)
2. Verifica coverage contra el mínimo definido en `openspec/config.yaml`
3. Audita requisito por requisito de `spec.md`
4. Emite veredicto: COMPLIANT / NON-COMPLIANT

Si NO pasa → vuelve a `sdd-apply` para corregir.

---

## TDD test-first (Strict TDD Mode)

```
1. RED: Escribe el test primero — el test falla
2. GREEN: Escribe el código mínimo que lo haga pasar
3. REFACTOR: Mejora el código sin romper tests
4. Siguiente requisito → repetir
```

El test se escribe **ANTES** del código.

---

## ⚠️ Aclaración: el orden real (evitar confusiones)

### Lo que NO es TDD

```
✗ 1. Se define la feature
✗ 2. Se implementa TODO el código de la feature
✗ 3. Luego se crea el test
✗ 4. El test evalúa la feature ya terminada
```

### Lo que SÍ es TDD (test-first)

```
✓ 1. spec.md define la feature → "El login debe retornar JWT"
        ↓
✓ 2. sdd-apply escribe el TEST PRIMERO, basado en spec.md  (RED → falla)
        ↓
✓ 3. sdd-apply escribe el código MÍNIMO para que el test pase  (GREEN)
        ↓
✓ 4. Refactoriza
        ↓
✓ 5. sdd-verify ejecuta todos los tests y valida contra spec.md
```

### Puntos clave

| Creencia común | Realidad |
|----------------|----------|
| "Primero se crea la feature completa, luego el test" | ❌ El test se escribe ANTES del código |
| "El test se genera automáticamente" | ❌ El agente lo escribe manualmente guiado por spec.md |
| "El test evalúa la feature ya hecha" | ✅ Sí, pero el test se escribió ANTES |

### Analogía

```
Como construir un examen ANTES de la clase:

1. spec.md  = "El plan de estudios" (qué debe saber el alumno)
2. El test  = "El examen" (se escribe ANTES de enseñar)
3. El código = "La clase" (se escribe para que el alumno apruebe el examen)
4. sdd-verify = "Aplicar el examen final"
```

---

## ¿Quién hace qué?

| Fase | Acción |
|------|--------|
| `sdd-apply` | **Genera** tests (TDD: RED → GREEN → refactor) |
| `sdd-verify` | **Ejecuta** tests + valida contra spec |
| `sdd-archive` | Cierra el ciclo si verify pasó |

---

## Referencias

- [[openspec]] — Donde se guarda config de testing
- [[sdd]] — Spec-Driven Development
- [[organic-routing]] — Cómo se elige la ruta de implementación
