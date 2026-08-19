# Índice de Aprendizaje - Gentle-AI

> Documentación de estudio sobre la herramienta Gentle-AI. Cada nota atómica representa una duda resuelta o concepto entendido.

---

## Conceptos Fundamentales

- Visión General y Arquitectura — Resumen de la arquitectura de alto nivel de Gentle-AI y sus componentes principales.
  → Nota: [[01-arquitectura-general]]

- Organic Routing — Cómo Gentle-AI decide automáticamente entre implementación directa, delegated, o SDD.
  → Nota: [[organic-routing]]

- Receipt-Driven Development (RDD) — Sistema de revisión bounded con receipts que validan los delivery gates.
  → Nota: [[rdd]]

- Spec-Driven Development (SDD) — Workflow de 9 fases para planificar y ejecutar cambios sustanciales.
  → Nota: [[sdd]]

- Engram - Memoria Persistente — Sistema de memoria que permite al agente recordar decisiones entre sesiones.
  → Nota: [[engram]]

- Skills System — Archivos SKILL.md que codifican mejores prácticas y patrones de desarrollo.
  → Nota: [[skills]]

- Commands vs Skills — Cómo Commands y Skills trabajan juntos en los flujos de SDD.
  → Nota: [[commands-vs-skills]]

- Tests, TDD y SDD — Cómo los tests se generan en apply y se validan en verify.
  → Nota: [[tests-tdd-sdd]]

- sdd-init — Inicialización del contexto SDD: detecta stack, testing, skills y convenciones.
  → Nota: [[sdd-init]]

- Plugins — Extensiones TypeScript que añaden funcionalidad extra a OpenCode.
  → Nota: [[plugins]]

---

## Componentes del Sistema

- Agentes Soportados — Lista de agentes de codificación AI que Gentle-AI puede configurar.
  → Nota: [[agentes-soportados]]

- AGENTS.md Anidados — Gentle-AI no soporta agents.md anidados en subdirectorios.
  → Nota: [[agents-md-anidados]]

- AGENTS.md Config vs Raíz — Diferencia entre el AGENTS.md en .config/opencode/ y el de la raíz del proyecto.
  → Nota: [[agents-md-config-vs-raiz]]

- OpenSpec — Sistema de archivos para SDD, estructura de directorios y flujos típicos.
  → Nota: [[openspec]]

- Comandos CLI — Guía de los comandos disponibles en gentle-ai (install, doctor, sync, etc.).
  → Nota: [[cli-commands]]

---

## Guías de Uso

- Persona y Profiles — Diferencia entre la personalidad del agente y la configuración de modelos IA.
  → Nota: [[persona-y-profiles]]

- Sub-Agentes Custom — Cómo integrar mis sub-agentes de .opencode/agents con Gentle Orchestrator.
  → Nota: [[subagentes-custom]]

- Global vs Local — Cuándo instalar gentle-ai globalmente vs por proyecto.
  → Nota: [[instalacion-global-vs-local]]

- Ejecutar Gentle-AI — TUI vs CLI, scope (global/local) y binario.
  → Nota: [[ejecutar-gentle-ai]]

- Flujo de Trabajo Diario — Cómo trabajar día a día con Gentle-AI configurado.
  → Nota: [[flujo-trabajo]]

- Análisis Integración Bóveda — Evaluación de integrar gentle-ai en la bóveda de Obsidian.
  → Nota: [[analisis-integracion-boveda]]

- Plan Migración Bóveda — Plan detallado para migrar al gentle-orchestrator en la bóveda.
  → Nota: [[plan-migracion-boveda]]

---

## Próximos Temas por Explorar

- [ ] Entender el Skill Registry en detalle
- [ ] Agentes adapters — cómo se configura cada agente
- [ ] MCP servers — configuración de Context7, Notion, Jira

---

*Última actualización: 2026-08-17*
