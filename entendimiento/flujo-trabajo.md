# Flujo de Trabajo Diario

## El flujo core

```
1. Install y configuración inicial
   gentle-ai install (o con --scope=workspace)

2. Usa la ruta de implementación más pequeña
   Direct inline → Delegated direct → SDD (solo si reduce ambigüedad)

3. Construye con disciplina
   /sdd-init detecta testing → Strict TDD Mode
   SDD apply trabaja test-first

4. Revisa un candidato
   (si RDD está habilitado) review bounded + receipt

5. Upgrade + sync
   gentle-ai upgrade && gentle-ai sync
```

---

## Sesión típica

### Inicio

```
1. Abres OpenCode en tu proyecto
2. Plugin refresca el skill registry automáticamente
3. (Si no hay contexto SDD) el orquestador corre /sdd-init
4. Engram inyecta contexto de sesiones previas (mem_context)
```

### Durante

```
1. Pides un cambio
2. El agente elige la ruta más pequeña:
   - 1-3 archivos → direct inline
   - 4+ archivos → delegated
   - Ambigüedad sustancial → ofrece SDD
3. (SDD) /sdd-new → explore → propose → spec → design → tasks → apply
4. El agente guarda decisiones en Engram (mem_save)
```

### Final

```
1. (Si RDD activo) revisión del candidato → receipt
2. Commit (tú controlas el push desde VS Code)
3. El agente cierra con mem_session_summary
```

---

## Reglas diarias (de este proyecto)

| Regla | Detalle |
|-------|---------|
| **No auto-push** | El usuario controla push desde VS Code |
| **Preguntar antes de crear notas** | Siempre confirmar |
| **No regenerar notas borradas** | Si no existe, fue borrada a propósito |
| **Wikilinks solo a notas existentes** | Verificar con glob antes de modificar índice |

---

## Comandos frecuentes

```bash
gentle-ai doctor                      # ¿todo bien?
gentle-ai sync                        # refrescar assets
gentle-ai skill-registry refresh      # reconstruir registry
/sdd-new "descripción del cambio"     # iniciar SDD
/sdd-status                           # estado de cambios
```

---

## Backup automático

Cada install/sync/upgrade hace snapshot:
- Comprimido (tar.gz)
- Deduplicado
- Auto-prune (mantiene 5)
- Pin con tecla `p` en TUI

---

## Resumen

```
Día a día:
├── Abre OpenCode → contexto listo (registry + Engram)
├── Pide cambios → elige la ruta más pequeña
├── SDD cuando hace falta → TDD en apply
├── RDD si está activo → receipt
└── Tú controlas el push
```

---

## Referencias

- [[organic-routing]] — Elegir la ruta de implementación
- [[sdd]] — Workflow de planificación
- [[rdd]] — Revisión bounded
- [[cli-commands]] — Comandos disponibles
- [[engram]] — Memoria persistente
