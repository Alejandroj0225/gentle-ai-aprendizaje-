# Pipeline de Aprendizajes — Bóveda Personal y Puente con Proyectos

> Documentación del sistema de aprendizaje efectivo de la bóveda personal (`C:\Users\alela\obsidian\ALEJANDRO J LOPEZ`) y el diseño del puente para exportar aprendizajes desde proyectos externos (ej. el plugin de Obsidian).

---

## Sistema actual de la bóveda (ya funciona)

### Pipeline de flashcards → Anki

```
Sesión práctica (proyecto o ejercicio)
  → el usuario escribe "generar flashcard"
  → skill generador-de-flashcards
  → nota diaria: "DD-MM-YYYY - Flashcards - <Área>.md"
    (en Metas/2026/Get a job/Elementos de - Get a job/Aprendizajes temporales - Get a job/)
  → skill exportador-de-flashcards-csv → script export_csv.js
  → CSV consolidado (sin encabezado, 2 columnas, comillas dobles, escapes para Anki)
  → importación en Anki
```

### Skills clave existentes en la bóveda

| Skill | Función |
|---|---|
| `generador-aprendizajes-temporales-meta` | Crea la carpeta `Aprendizajes temporales - [Meta]/` con su nota-índice (MOC) |
| `generador-de-flashcards` | Genera flashcards en vivo desde el par de turnos (pregunta del usuario + respuesta de IA) |
| `exportador-de-flashcards-csv` | Convierte nota Markdown → CSV Anki (usa `scripts/export_csv.js`) |

### Formato de flashcard (innegociable para Anki)

- Una sola nota por día y área: `DD-MM-YYYY - Flashcards - <Área>.md`
- Estructura plana (sin subcategorías `## Tecnología`)
- Cada tarjeta: `### <N>` + `#### Anverso` + `#### Reverso`
- Frontmatter: `Fecha`, `tags` (flashcard, aprendizaje, área), `tipo: flashcard`
- **Regla de oro**: generalización obligatoria — la tarjeta expresa el concepto subyacente, NUNCA atada a un ejercicio/proyecto concreto (nombres de funciones, variables o archivos propios). Ejemplo de código genérico (`datos`, `clave`, `config`).
- Reverso estructurado: **El Qué** (concepto conciso) + **El Porqué** (fundamento) + **El Cómo** (código fenced con lenguaje)
- CSV Anki: sin fila de encabezado, 2 columnas, cada campo entre comillas dobles, comillas internas duplicadas, saltos de línea como `<br>`, HTML escapado (`<` → `&lt;`, `>` → `&gt;`), MathJax convertido (`$...$` → `\(...\)`)

### Contexto operativo de la bóveda

- La bóveda opera con un **orquestador único** (`.opencode/agents/orquestador.md`) que enruta por AGENTS.md raíz + AGENTS.md local de meta (alcances)
- La meta activa para este proyecto: `Metas/2026/Get a job/` — alcance **Sesión Práctica Guiada** (personalidad `sesion-practica-get-a-job`), con carpeta secundaria de Aprendizajes temporales
- Las skills son "autoridad ejecutora" y se registran en la Matriz de skills (`Sistemas/Sistemas de funcionamiento de la bóveda/Matriz de skills.md`)

---

## El puente: exportar aprendizajes desde proyectos externos

### Problema

El proyecto del plugin vive **fuera de la bóveda** (ej. carpeta de proyectos de desarrollo). Cuando durante la construcción del plugin surja un aprendizaje (concepto, metodología, arquitectura, duda resuelta), se debe poder exportar una flashcard a la bóveda sin fricción.

### Decisión: skill autocontenida (NO subagente, NO lectura de AGENTS.md)

**Recomendación: crear una skill nueva en el proyecto** (`skills/exportar-aprendizajes-boveda/`). Razones:

1. La bóveda ya define "Skills como autoridad ejecutora" — un subagente rompería la convención
2. La tarea es determinística: escribir notas con formato conocido en ruta conocida
3. Reutiliza lo existente: el formato de `generador-de-flashcards` y el script `export_csv.js`
4. Coherente con la Matriz de skills

### Ruta dentro de la skill (decisión tomada)

La ruta de la bóveda vive **dentro de la carpeta de la skill**, en un `config.json`, NO en el AGENTS.md del proyecto:

```
skills/exportar-aprendizajes-boveda/
├── SKILL.md      → flujo, formato, regla de oro
└── config.json   → { "bovedaRuta": "C:\\Users\\alela\\obsidian\\ALEJANDRO J LOPEZ",
                      "carpetaDestino": "Metas/2026/Get a job/Elementos de - Get a job/Aprendizajes temporales - Get a job",
                      "area": "Programación" }
```

**Por qué**: el usuario tiene una sola bóveda con ruta estable → simplicidad máxima, un solo lugar para actualizar, cero fricción. AGENTS.md sirve para reglas globales, no para datos concretos. (Si algún día hubiera multi-bóveda o multi-máquina, recién ahí tendría sentido externalizar.)

### Flujo de la skill

```
Input:  "aprendizaje: <concepto aprendido> / <duda resuelta>" + área
Proceso:
  1. Leer config.json (ruta de bóveda, carpeta destino, área)
  2. Validar que la ruta existe (Test-Path) — avisar si no
  3. Aplicar el MISMO formato de generador-de-flashcards
     (generalización obligatoria, ### N, #### Anverso/Reverso, frontmatter)
  4. Escribir/append en "DD-MM-YYYY - Flashcards - <Área>.md"
  5. Si la nota no existía → indexar wikilink en el MOC (Aprendizajes temporales - Get a job.md)
  6. Opcional: invocar export_csv.js → generar CSV directo
Output: nota flashcard creada/actualizada (+ CSV opcional) en la bóveda
```

### Dato clave

Obsidian detecta automáticamente los archivos que aparecen en las carpetas de la bóveda (aunque los escriba otro agente desde afuera). El "puente" es el agente escribiendo notas con el formato correcto — no requiere nada especial del plugin.

### Próximos pasos

- [ ] Crear la skill `exportar-aprendizajes-boveda` en el proyecto del plugin (cuando se cree el proyecto)
- [ ] Registrar en la Matriz de skills de la bóveda si el orquestador debe conocerla
- [ ] Definir si la exportación incluye CSV directo o solo la nota .md

---

*Nota creada: 2026-08-19*