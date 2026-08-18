# AGENTS.md — Proyecto de Aprendizaje Gentle-AI

> Este archivo define las convenciones y reglas para el trabajo de aprendizaje sobre Gentle-AI.

---

## Reglas de Documentación

### Wikilinks

- **Solo crear wikilinks** si la nota destino ya existe
- **No crear wikilinks** a notas que aún no se han creado
- **Antes de crear un wikilink**, verificar que el archivo `.md` existe

### Creación de Notas

#### Escenario 1: Nuevo tema

Cuando se resuelve una duda sobre un tema que **NO tiene precedente** en la documentación:

1. Crear la nota atómica con el contenido
2. Buscar la sección correspondiente en el índice
3. Agregar como **bullet de nivel más alto** (sin anidamiento)

#### Escenario 2: Profundización de tema existente

Cuando se resuelve una duda sobre un tema que **YA tiene estructura definida**:

1. Identificar la nota o sección relacionada existente
2. Crear la nueva nota como extensión/anidamiento de la existente
3. Agregar como sub-item dentro de la estructura del tema padre

### Siempre preguntar

Antes de crear cualquier nota, **preguntar siempre**:

> "¿Quieres que cree una nota con esta información?"

El usuario decide si crear o no la nota.

---

## Reglas de Workflow

### Git

- **No hacer push automático** a Git
- El usuario controla el push desde VS Code Source Control
- Hacer `git add` y `git commit` según necesidad, pero **no hacer push**

### Memoria (Engram)

- Guardar decisiones importantes con `mem_save`
- Usar topic_keys estables para temas que evolucionan
- Al final de sesión, hacer `mem_session_summary`

---

## Convenciones de Nombre

- Notas atómicas: nombre descriptivo en español, guiones en lugar de espacios
- Ejemplo: `governanza-reglas.md`, `agents-md-anidados.md`
- Índice: `indice.md`

---

## Estructura del Proyecto

```
entendimiento/
├── indice.md              ← Punto de entrada
├── 01-arquitectura-general.md
├── agentes-opencode.md
├── agents-md-anidados.md
├── gobernanza-reglas.md
└── [notas futuras...]
```

---

*Última actualización: 2026-08-17*
