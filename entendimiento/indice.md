# Índice de Aprendizaje - Gentle-AI

> Documentación de estudio sobre la herramienta Gentle-AI. Cada nota atómica representa una duda resuelta o concepto entendido.

---

## Conceptos Fundamentales

- Visión General y Arquitectura — Resumen de la arquitectura de alto nivel de Gentle-AI y sus componentes principales.
  → Nota: [[01-arquitectura-general]]

- Organic Routing — Cómo Gentle-AI decide automáticamente entre implementación directa, delegated, o SDD.
  → Nota: (por crear)

- Receipt-Driven Development (RDD) — Sistema de revisión bounded con receipts que validan los delivery gates.
  → Nota: (por crear)

- Spec-Driven Development (SDD) — Workflow de 9 fases para planificar y ejecutar cambios sustanciales.
  → Nota: (por crear)

- Engram - Memoria Persistente — Sistema de memoria que permite al agente recordar decisiones entre sesiones.
  → Nota: [[engram]]

- Skills System — Archivos SKILL.md que codifican mejores prácticas y patrones de desarrollo.
  → Nota: [[skills]]

---

## Componentes del Sistema

- Agentes Soportados — Lista de agentes de codificación AI que Gentle-AI puede configurar.
  → Nota: (por crear)

- AGENTS.md Anidados — Gentle-AI no soporta agents.md anidados en subdirectorios.
  → Nota: [[agents-md-anidados]]

- AGENTS.md Config vs Raíz — Diferencia entre el AGENTS.md en .config/opencode/ y el de la raíz del proyecto.
  → Nota: [[agents-md-config-vs-raiz]]

- OpenSpec — Sistema de archivos para SDD, estructura de directorios y flujos típicos.
  → Nota: [[openspec]]

- Estructura del Proyecto — Organización del código fuente y directorios principales.
  → Nota: (por crear)

- Comandos CLI — Guía de los comandos disponibles en gentle-ai (install, doctor, sync, etc.).
  → Nota: (por crear)

---

## Guías de Uso

- Flujo de Trabajo Diario — Cómo trabajar día a día con Gentle-AI configurado.
  → Nota: (por crear)

- Configuración de Fork — Cómo mantener sincronizado tu fork con el repositorio upstream.
  → Nota: (por crear)

---

## Próximos Temas por Explorar

- [ ] Profundizar en Organic Routing
- [ ] Detallar las 9 fases de SDD
- [ ] Entender el Skill Registry
- [ ] Revisión detallada de RDD (receipts, lenses, corrección bounded)
- [ ] Agentes adapters — cómo se configura cada agente
- [ ] MCP servers — configuración de Context7, Notion, Jira

---

*Última actualización: 2026-08-17*
