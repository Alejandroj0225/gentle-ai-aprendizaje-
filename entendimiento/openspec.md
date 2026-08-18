# OpenSpec — Sistema de Archivos para SDD

## ¿Qué es OpenSpec?

**No es una herramienta ni un servicio** — es una **convención de directorios** que define dónde se guardan los artifacts generados durante el workflow SDD (Spec-Driven Development).

---

## Estructura de Directorios

```
mi-proyecto/
├── openspec/                  ← SIEMPRE en la raíz del proyecto
│   ├── config.yaml            ← Configuración del proyecto
│   ├── specs/                 ← Specs activos (en desarrollo)
│   │   └── <feature-name>/
│   │       ├── proposal.md
│   │       ├── spec.md
│   │       ├── design.md
│   │       └── tasks.md
│   └── changes/
│       └── archive/           ← Specs completados
│           └── <feature-name>-<date>/
│               ├── spec.md
│               ├── design.md
│               └── tasks.md
├── src/
├── tests/
└── ...
```

---

## ¿Qué guarda cada directorio?

### `config.yaml`

Configuración general del proyecto:

```yaml
stack: typescript-react
architecture: hexagonal
persistence: openspec  # o "engram" o "hybrid"

strict_tdd: true

conventions:
  commits: conventional-commits
  branches: feature/ticket-description

rules:
  apply:
    tdd: true
    test_command: "npm test"
  verify:
    coverage: 80
```

**Se usa durante TODO el workflow SDD** — es como el "manual de instrucciones" del proyecto.

### `specs/`

Contiene los specs de features **en desarrollo activo**:

```
specs/
└── auth-jwt/
    ├── proposal.md     → "¿Qué queremos hacer?"
    ├── spec.md        → "¿Qué debe hacer exactamente?"
    ├── design.md      → "¿Cómo lo vamos a diseñar?"
    └── tasks.md       → "¿Cuáles son las tareas?"
```

### `changes/archive/`

Contiene specs de features **ya completados**:

```
changes/archive/
└── auth-jwt-2026-08-17/
    ├── spec.md
    ├── design.md
    └── tasks.md
```

---

## Flujo Típico con SDD y OpenSpec

### Paso 1: `sdd-init` (una sola vez por proyecto)

```
$ gentle-ai install --scope=workspace --agents opencode
o
$ /sdd-init

↓

Gentle-AI detecta:
- package.json → Node.js
- stack: typescript
- testing: vitest

↓

Crea (si no existe):

openspec/
├── config.yaml        ← con el stack detectado
├── specs/
└── changes/
```

---

### Paso 2: Iniciar SDD para una nueva feature

```
$ /sdd-new
o
$ sdd-propose

↓

Agente detecta que necesitas una feature
y te pregunta si quieres usar SDD

↓

Crea en specs/:

specs/
└── nueva-feature/
    ├── proposal.md     → creado
    ├── spec.md        → por hacer
    ├── design.md      → por hacer
    └── tasks.md       → por hacer
```

---

### Paso 3: `sdd-propose` → Define la propuesta

```
Propuesta: "Agregar autenticación JWT"

↓

Crea:

specs/auth-jwt/proposal.md
---
## ¿Qué?
Agregar sistema de autenticación con JWT

## ¿Por qué?
Necesitamos proteger las rutas de la API

## Scope inicial
- Login con email/password
- Generación de JWT
- Middleware de verificación
```

---

### Paso 4: `sdd-spec` → Escribe la especificación

```
Agente lee config.yaml para convenciones
Crea spec.md con requisitos exactos

↓

specs/auth-jwt/spec.md
---
## Functional Requirements
- FR-1: Login acepta email y password
- FR-2: Login retorna JWT válido por 24h
- FR-3: Refresh token incluido

## Non-Functional Requirements
- NFR-1: Tokens firmados con RS256
- NFR-2: Passwords hasheados con bcrypt
```

---

### Paso 5: `sdd-design` → Diseño técnico

```
specs/auth-jwt/design.md
---
## Architecture
- AuthService en src/auth/
- JWT library: jsonwebtoken
- Password hashing: bcrypt

## API Endpoints
- POST /auth/login
- POST /auth/refresh
- GET /auth/me
```

---

### Paso 6: `sdd-tasks` → Lista de tareas

```
specs/auth-jwt/tasks.md
---
- [ ] Crear AuthService
- [ ] Implementar login endpoint
- [ ] Implementar refresh token
- [ ] Crear middleware de verificación
- [ ] Escribir tests unitarios
- [ ] Escribir tests de integración
```

---

### Paso 7: `sdd-apply` → Implementación

```
El sub-agente de apply:
1. Lee config.yaml → sabe que debe usar TDD
2. Lee spec.md → sabe qué implementar
3. Lee tasks.md → sabe por dónde empezar
4. Implementa y marca cada task con [x]
```

---

### Paso 8: `sdd-verify` → Verificación

```
Verifica que:
- El código cumple spec.md
- Coverage >= 80%
- Todos los tests pasan
```

---

### Paso 9: `sdd-archive` → Archivar

```
Mueve de specs/ a changes/archive/:

specs/auth-jwt/  →  (se elimina)

changes/archive/
└── auth-jwt-2026-08-17/
    ├── spec.md
    ├── design.md
    └── tasks.md
```

---

## ¿Cuándo se usa OpenSpec?

| Usas SDD? | Usa OpenSpec? |
|-----------|---------------|
| Sí | ✅ Sí — todos los artifacts van aquí |
| No | ❌ No — solo funciona Engram para decisiones |

**OpenSpec es dependiente de SDD** — sin workflow SDD, no hay OpenSpec.

---

## Diferencia entre OpenSpec y Engram

| Aspecto | OpenSpec | Engram |
|---------|----------|--------|
| ¿Qué es? | Directorio de archivos | Sistema de memoria |
| ¿Qué guarda? | Specs, designs, tasks | Decisiones, bugs, convenciones |
| ¿Persiste entre sesiones? | ✅ Sí (en archivos) | ✅ Sí (en base de datos) |
| ¿Quién lo usa? | SDD phases | Cualquier fase |
| ¿Se comparte entre agentes? | ✅ Sí (archivos) | ✅ Sí (vía Engram MCP) |

---

## ¿Y si no usas SDD?

```
Sin SDD:
├── OpenSpec → NO se usa
├── Engram → SÍ se usa para guardar decisiones
└── AGENTS.md → SÍ define convenciones
```

---

## Modos de Persistencia

En `config.yaml` puedes especificar:

```yaml
persistence: openspec  # Solo archivos
persistence: engram    # Solo Engram
persistence: hybrid    # AMBOS — archivos + Engram
```

| Modo | Ventaja | Desventaja |
|------|---------|------------|
| `openspec` | Todo versionado en git | Menos flexible |
| `engram` | Más flexible, busca rápido | No versionado |
| `hybrid` | Lo mejor de ambos | Más complejo |

---

## Comandos relacionados

```bash
# Inicializar OpenSpec
gentle-ai install
# o
/sdd-init

# Ver estado de specs
gentle-ai sdd-status

# Forzar refresh del skill registry
gentle-ai skill-registry refresh
```

---

## Resumen

```
OpenSpec = el "disco duro" del SDD

┌─────────────────────────────────────────────────────────────┐
│  config.yaml → Manual de instrucciones del proyecto        │
│                                                              │
│  specs/      → Feature que estás desarrollando              │
│                                                              │
│  changes/    → Features que ya completaste                   │
└─────────────────────────────────────────────────────────────┘
```

---

## Notas Relacionadas

- [[sdd]] — Spec-Driven Development (flujo completo)
- [[agents-md-config-vs-raiz]] — Diferencia entre AGENTS.md
- [[ gobernanza-reglas]] — Reglas de gobernanza del proyecto
