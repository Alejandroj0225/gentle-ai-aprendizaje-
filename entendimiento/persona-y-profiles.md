# Persona vs Profiles — Conceptos Diferentes

## Resumen

| Concepto | Qué es | Cambia... |
|----------|--------|------------|
| **Persona** | Personalidad del agente | CÓMO habla, su tono, estilo |
| **Profiles** | Configuración de modelos IA | QUÉ modelo usa en cada fase SDD |

---

## Persona

### ¿Qué es?
La **personalidad** que Gentle-AI configura para el agente. Define cómo se comporta, habla y responde.

### Modos de Persona

| Modo | Descripción |
|------|-------------|
| **Gentleman Mode** | Senior Architect mentor que enseña y desafía |
| **Neutral Mode** | Profesional, sin personalidad, respuestas directas |
| **Custom** | Tú defines la persona completa |

### Ejemplo de Gentleman Mode:

```markdown
## Personality

Senior Architect, 15+ years experience, GDE & MVP. 
Passionate teacher who genuinely wants people to learn and grow.

## Tone

Passionate and direct, but from a place of CARING. When someone is wrong: 
(1) validate the question makes sense, 
(2) explain WHY it's wrong with technical reasoning, 
(3) show the correct way with examples.
```

### ¿Dónde se configura?
- Durante `gentle-ai install` → selección de persona
- Se inyecta en `AGENTS.md` o `CLAUDE.md` según el agente

### ¿Qué afecta?
- ✅ Tono de voz en respuestas
- ✅ Estilo de comunicación
- ✅ Cómo corrige errores
- ✅ Cuándo enseña vs solo completa
- ❌ NO afecta código generado
- ❌ NO afecta modelos de IA usados

---

## Profiles (OpenCode)

### ¿Qué es?
Configuración de **qué modelo de IA** se usa en cada fase SDD.

### ¿Para qué sirven?
Optimizar costos y calidad:
- Fases complejas (design, verify) → modelos potentes
- Fases simples (spec, tasks) → modelos económicos

### Estructura de un Profile

```json
{
  "profile": "cheap",
  "agents": {
    "gentle-orchestrator": "claude-sonnet",
    "sdd-apply": "claude-haiku",
    "sdd-verify": "claude-haiku",
    "sdd-design": "claude-opus",
    "sdd-spec": "claude-sonnet"
  }
}
```

### Profiles típicos

| Profile | Costo | Uso |
|---------|--------|-----|
| `cheap` | $0-1 | Exploración, tareas simples |
| `recommended` | $$ | Trabajo diario |
| `powerful` | $$$ | Diseño, revisión rigurosa |

### ¿Dónde se guardan?

**Opción A: Inside opencode.json** (default)
```json
{
  "agent": {
    "sdd-orchestrator-cheap": { ... },
    "sdd-apply-cheap": { ... }
  }
}
```

**Opción B: External profiles** (archivos separados)
```
~/.config/opencode/profiles/
├── cheap.json
├── recommended.json
└── powerful.json
```

### ¿Cómo se activan?
Con **Tab** dentro de OpenCode → selecciona el perfil deseado.

---

## Diferencias Clave

| Aspecto | Persona | Profiles |
|---------|---------|----------|
| Cambia... | CÓMO habla | QUÉ modelo usa |
| Afecta código? | No | No |
| Costo? | No | Sí (modelos) |
| Se cambia con Tab? | No | Sí |
| Configurado por | Gentle-AI install | OpenCode profiles TUI |

---

## ¿Cuándo usar cada uno?

| Necesitas... | Solución |
|-------------|----------|
| Que el agente hable formal | Persona: Neutral |
| Que el agente enseñe | Persona: Gentleman |
| Ahorrar en fases simples | Profile: cheap |
| Máxima calidad en diseño | Profile: powerful |
| Mezcla de ambos | Ambos |

---

## Notas Relacionadas

- [[skills]] — Sistema de skills
- [[sdd]] — Spec-Driven Development
- [[agentes-opencode]] — Agentes en OpenCode
