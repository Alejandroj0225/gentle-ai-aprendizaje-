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

### Notas borradas

- **Si una nota no existe en el filesystem** pero el índice dice "por crear", **NO regenerarla**
- Esto significa que el usuario la borró intencionalmente y no quiere que se vuelva a crear

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

## Flujo de Trabajo con Notas

### Antes de crear cualquier nota

**SIEMPRE leer el índice (`indice.md`) primero** para determinar:

1. **¿El tema ya existe?** → Escenario 2 (profundización)
2. **¿Es un tema completamente nuevo?** → Escenario 1 (nuevo tema)

### Escenario 1: Nuevo tema

Cuando la duda es sobre un tema que **NO tiene precedente**:

1. Leer el índice para identificar la sección correspondiente
2. Crear la nota atómica con el contenido
3. Agregar como **bullet de nivel más alto** en el índice
4. El wikilink se crea solo cuando la nota existe

### Escenario 2: Profundización

Cuando la duda es sobre un tema que **YA tiene estructura definida**:

1. Leer el índice para encontrar la sección/tema padre
2. Crear la nota como extensión del tema existente
3. Agregar como **sub-bullet** dentro de la estructura del tema padre

---

## Convenciones de Nombre

- Notas atómicas: nombre descriptivo en español, guiones en lugar de espacios
- Ejemplo: `agents-md-anidados.md`, `openspec.md`
- Índice: `indice.md`

---

*Última actualización: 2026-08-17*
