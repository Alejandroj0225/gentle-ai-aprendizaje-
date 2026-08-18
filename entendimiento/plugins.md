# Plugins en OpenCode

## ¿Qué son?

Plugins son **extensiones de código TypeScript** que añaden funcionalidad extra a OpenCode. Se cargan al iniciar y corren automáticamente.

---

## Ubicación

```
.config/opencode/plugins/
├── skill-registry.ts           ← Refresca skill registry al iniciar
├── opencode-review-transport.ts ← Conecta review con gentle-ai
├── sdd-task-result-artifacts.ts ← Maneja artifacts de tareas SDD
└── model-variants.ts          ← Variantes de modelos
```

---

## Plugins instalados

### `skill-registry.ts`

Refresca el skill registry cuando OpenCode inicia.

```typescript
export const SkillRegistryPlugin: Plugin = async (input) => {
  // Se ejecuta al iniciar
  // Detecta si está en un proyecto válido
  // Ejecuta gentle-ai skill-registry refresh
}
```

**Propósito:** Asegura que el registry esté actualizado al arrancar.

---

### `opencode-review-transport.ts`

Conecta el sistema de review de gentle-ai con OpenCode.

```typescript
const REVIEW_AGENTS = new Set([
  "review-risk",
  "review-resilience", 
  "review-readability",
  "review-reliability",
  "review-refuter",
  "review-validator"
])
```

**Propósito:** Permite que los agentes de review de RDD funcionen en OpenCode.

---

### `sdd-task-result-artifacts.ts`

Maneja los artifacts generados por las tareas SDD.

**Propósito:** Procesa y formatea los resultados de sub-agentes SDD.

---

### `model-variants.ts`

Añade variantes de modelos de IA.

**Propósito:** Permite usar diferentes variantes de modelos para diferentes tareas.

---

## Cómo funcionan

```typescript
export const NombrePlugin: Plugin = async (input) => {
  // Se ejecuta cuando OpenCode inicia
  // Añade funcionalidad extra
}
```

Son **código compilado** (TypeScript → JavaScript) que OpenCode carga como plugin.

---

## Diferencia con otras extensiones

| Extensión | Qué es | Cuándo se ejecuta |
|-----------|--------|-------------------|
| **Plugin** | Código TypeScript compilado | Al iniciar OpenCode |
| **Skill** | Instrucciones de comportamiento | Cuando agente detecta trigger |
| **Command** | Slash commands | Cuando tú lo invocas |
| **Sub-agente** | Definición de agente | Cuando se necesita |

---

## ¿Quién instala los plugins?

Gentle-AI instala estos plugins cuando ejecutas `gentle-ai install --agents opencode`.

---

## Estructura general

```
Plugins = "Código extra que corre al iniciar"
├── Añaden funcionalidad a OpenCode
├── Integran con gentle-ai
└── Se cargan automáticamente
```

---

## Analogía

```
Plugin = "App que se abre automáticamente al prender la computadora"
Skill = "Libro de reglas que sigues cuando trabajas"
Command = "Comando que escribes en la terminal"
Sub-agente = "Persona que contratas para hacer algo específico"
```

---

## Para qué sirve en la práctica

| Problema | Solución con Plugin |
|----------|---------------------|
| Skill registry desactualizado | `skill-registry.ts` lo refresca |
| Review no funciona en OpenCode | `opencode-review-transport.ts` los conecta |
| Resultados SDD mal formateados | `sdd-task-result-artifacts.ts` los procesa |

---

## Referencias

- [[commands-vs-skills]] — Commands vs Skills
- [[skills]] — Sistema de skills
- [[subagentes-custom]] — Sub-agentes en OpenCode
