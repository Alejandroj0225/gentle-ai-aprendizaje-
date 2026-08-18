# Índice de Aprendizaje - Gentle-AI

> Documentación de estudio sobre la herramienta Gentle-AI. Cada nota atómica representa una duda resuelta o concepto entendido.

---

# Conceptos Fundamentales

## [[01-arquitectura-general|Visión General y Arquitectura]]
Resumen de la arquitectura de alto nivel de Gentle-AI y sus componentes principales.

## [[organic-routing|Organic Routing]]
Cómo Gentle-AI decide automáticamente entre implementación directa, delegada, o SDD.

## [[rdd|Receipt-Driven Development (RDD)]]
Sistema de revisión bounded con receipts que validan los delivery gates.

## [[sdd|Spec-Driven Development (SDD)]]
Workflow de 9 fases para planificar y ejecutar cambios sustanciales.

## [[engram|Engram - Memoria Persistente]]
Sistema de memoria que permite al agente recordar decisiones entre sesiones.

## [[skills|Skills System]]
Archivos SKILL.md que codifican mejores prácticas y patrones de desarrollo.

---

# Componentes del Sistema

## [[agentes-soportados|Agentes Soportados]]
Lista de agentes de codificación AI que Gentle-AI puede configurar.

## [[agentes-opencode|Agentes en OpenCode]]
Cómo funciona el sistema de AGENTS.md y opencode.json en OpenCode.

## [[agents-md-anidados|AGENTS.md Anidados]]
Gentle-AI no soporta agents.md anidados en subdirectorios - es una característica de Cursor/Claude Code, no de OpenCode.

## [[estructura-proyecto|Estructura del Proyecto]]
Organización del código fuente y directorios principales.

## [[cli-commands|Comandos CLI]]
Guía de los comandos disponibles en gentle-ai (install, doctor, sync, etc.).

---

# Guías de Uso

## [[flujo-trabajo|Flujo de Trabajo Diario]]
Cómo trabajar día a día con Gentle-AI configurado.

## [[fork-setup|Configuración de Fork]]
Cómo mantener sincronizado tu fork con el repositorio upstream.

---

# Próximos Temas por Explorar

- [ ] Profundizar en `internal/cli/` — comandos
- [ ] Detallar las 9 fases de SDD
- [ ] Entender el Skill Registry
- [ ] Revisión detallada de RDD (receipts, lenses, corrección bounded)
- [ ] Agentes adapters — cómo se configura cada agente
- [ ] MCP servers — configuración de Context7, Notion, Jira

---

*Última actualización: 2026-08-17*
