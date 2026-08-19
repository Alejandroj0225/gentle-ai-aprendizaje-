# Plugin EMAI Command Center — Análisis

> Análisis de ingeniería inversa del plugin `emai-command-center` v0.1.0, referencia para el primer proyecto: un plugin propio de Obsidian adaptado a la bóveda personal.

---

## Qué es

EMAI Command Center es una **bóveda de Obsidian diseñada como "sistema operativo"** que incluye un plugin dashboard (panel de instrumentos). El plugin NO es un sistema cerrado: **lee notas normales de Obsidian** (frontmatter, checkboxes, eventos, logs) y las visualiza.

- **Ruta del plugin**: `C:\Users\alela\Desktop\EMAI Command Center\.obsidian\plugins\emai-command-center\`
- **Manifest**: id `emai-command-center`, v0.1.0, minAppVersion 1.8.0, isDesktopOnly: false
- **Descripción oficial**: "Custom command center view for the EMAI vault that behaves like a start page without resetting the workspace."

## Estructura de la bóveda (filosofía AI vs humano)

| Zona | Contenido |
|---|---|
| `00 Human/` | Notas del usuario: Inbox, Daily Notes, Tasks, Projects, Resources, People, Areas, Context, Templates, Content |
| `Machine/` | Lo que ayuda a la IA: `Workflows/` (50+ flujos: today, closeday, interview...), `Templates/`, `Personalization/`, `Logs/`, `Config/AIOS/` |
| `System/` | Documentación de uso y setup (Setup Guide, Command Center Map, Plugin Guide...) |

## Cómo está construido el plugin

### Hallazgo clave
El `main.js` (748 KB, 15.780 líneas) **NO está minificado**. Es un bundle de **68 módulos con nombres legibles**, generado por `scripts/build-plugin.cjs` (comentario en línea 1: *"edit maintainable sources, not this file"*). Es casi como tener el repo fuente.

### Mapa de módulos

- `main.source.js` — clase principal `EmaiCommandCenterPlugin extends Plugin`, vistas `CommandCenterView` y `MetricsDetailView` (ItemView)
- `runtime/` (34 módulos) — lógica de datos pura, SIN tocar Obsidian: dashboard-data, metrics, analytics, daily-drivers, time-tracking, capture, schedule, focus-context, chart-renderer, sparkline-renderer...
- `lib/ui/tabs/` — pestañas del dashboard: `today`, `metrics`, `content`, `intelligence`, `inbox`, `crm`, `settings`
- `lib/ui/top-navigation.js` — barra de navegación unificada
- `lib/routing/router.js` — `SchemaRouter`: decide a qué carpeta va cada tipo de nota
- `lib/setup/` — setup transaccional: schema-loader, setup-status, setup-proposal, schema-materializer (interview → propuesta → aprobar → aplicar → rollback)
- `lib/security/path-policy.js` — controla qué rutas puede escribir el plugin
- `lib/settings/` — preferencias de UI + guardado transaccional (transactional-save)
- `config/system-schema.json` — el "ADN" del sistema, embebido en el bundle y también como archivo en `Machine/Config/AIOS/system-schema.json`

### Superficie registrada en Obsidian
- 2 vistas: Command Center (dashboard) + Metrics
- 4 comandos: open-command-center, open-command-center-metrics, open-command-center-metrics-config, open-command-center-day-types-config
- Ribbon icon (configurable)
- Refresco: eventos del vault (modify/create/delete/rename) + intervalo de 60s

## Configuración (variables)

### data.json (settings del usuario)
- Rutas a notas del dashboard: `dashboardNotePath`, `calendarHubPath`, `metricsNotePath`, `dayTypesNotePath`, `inboxNotePath`, `backlogNotePath`, `eventLogPath`...
- Preferencias de apertura: `openTodayTarget`, `openContextPackTarget`... (valores: right/left/active)
- Permisos: `allowVaultWrites` (false por defecto), `allowInboxCaptureWrites`, `authorizedRouteIds`, `enabledRoutes`
- Métricas: `activeMetricSetId`, `metricsViewRange` (30d), `metricsViewSelectedMetricId`
- Visual: `dashboardStyle` (theme-native, navy-ember-boost, ghostrunner-spectrum, comic-pop), `showRibbonIcon`
- Estado: `interviewCompleted`, `activeTab`

### system-schema.json (el ADN)
- `folders` — mapa de carpetas (17 entradas)
- `plugins` — requeridos: calendar-beta, templater, obsidian-full-calendar, dataview, obsidian-style-settings, lean-terminal, emai-command-center
- `templates` — 17 plantillas con `requiredFields` (frontmatter obligatorio por tipo de nota)
- `noteTypes` — 16 tipos (inbox-capture, task, project, person, company, deal, activity, area, calendar-event, content-*, published-content, intelligence-output, daily-note)
- `properties` — diccionario de propiedades con tipo Obsidian (text, date, number, checkbox, tags...)
- `dailyNote` — carpeta, plantilla, formato de fecha y headings de la nota diaria (🎯 Today's Focus, 🐸 Frogs to Eat, ✅ Today's Tasks...)
- `metrics` — ~35 definiciones de métricas con: id, label, kind (stock/flow/score/state/cumulative), source (`frontmatter.X`, `events.X`, `tasks.X`, `vault.inbox_*`, `daily_drivers.*`, `time.*`), aggregation (last/sum/average/max), polarity, validRange, missingSemantics
- `routes` — 16 rutas de enrutamiento: noteType → destination, template, filenamePattern, allowedStatuses

## Loop de funcionamiento

```
/interview → configura métricas, Daily Drivers y day types (con agente IA)
/today     → crea/escribe la nota diaria con datos
Dashboard  → LEE las notas y las muestra (no escribe sin permiso aprobado)
/closeday  → cierra el día, registra wins/blockers/carry-overs en el log
```

## Fuentes de datos que lee el dashboard

- `frontmatter.*` — campos de notas diarias (youtube_subscribers, weight, skool_mrr...)
- `events.*` — notas de eventos de calendario en `00 Human/70 Context/Calendar Events/`
- `tasks.*` — checkboxes en notas diarias
- `vault.inbox_*` — notas de inbox en `00 Human/00 Inbox/`
- `daily_drivers.*` — eventos de Daily Drivers en `Machine/Logs/Command Center Events.md`
- `time.*` — eventos `time_logged` en `Machine/Logs/Command Center Events.md`

## Lecciones para el proyecto propio

1. **MVP pequeño**: no replicar los 68 módulos. Empezar con una vista que lea la nota diaria + 2-3 fuentes.
2. **Separar lógica pura de UI**: el patrón `runtime/` (cálculos testeables sin Obsidian) vs `lib/ui/` (render) es el secreto de la testabilidad.
3. **Schema-driven**: toda la configuración vive en un schema (JSON) + notas con frontmatter. El plugin interpreta, no hardcodea.
4. **Permisos explícitos**: el plugin solo escribe cuando el setup fue aprobado (`authorizedRouteIds`). Buen modelo de seguridad para no romper notas del usuario.
5. **Setup transaccional**: entrevista → propuesta → aprobar → aplicar → verificar → rollback. Patrón interesante para configurar sin destruir.
6. **El bundle no minificado es una ventaja**: se puede leer el código completo de los 68 módulos para aprender TypeScript y patrones de plugins de Obsidian.

---

*Nota creada: 2026-08-19*
