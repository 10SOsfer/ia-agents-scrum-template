# Protocolo de hotfix (bugs críticos en producción)

Cuando se detecta un bug crítico en producción fuera de un sprint activo:

---

## 1. Evaluar severidad con el usuario

| Severidad | Criterio | Acción |
|-----------|----------|--------|
| **Crítico** | Sistema caído, pérdida de datos, seguridad | Hotfix inmediato |
| **Alto** | Flujo principal roto, sin workaround | Hotfix en las próximas horas |
| **Medio o bajo** | Funcionalidad secundaria afectada | Registrar en `./backlog/bugs.md` y priorizar en el próximo sprint |

---

## 2. Si se activa el hotfix

1. OsferPO identifica el dev responsable (OsferDevFront o OsferDevBack) según el área del bug
2. El dev corrige **solo el bug** — sin refactorizar ni agregar funcionalidad
3. OsferQA revisa **solo el fix** — verificar que resuelve el bug sin introducir regresión
4. OsferOps despliega el fix siguiendo el proceso normal (staging → smoke tests → producción)
5. Registrar el incidente en `./backlog/incidents.md` con post-mortem

---

## 3. Documentación obligatoria

- Registrar en `./backlog/incidents.md` (causa, fix, tiempo de resolución)
- Agregar el bug a `./backlog/bugs.md` como cerrado
- En la próxima retrospectiva, analizar cómo prevenir recurrencia

**Regla**: un hotfix NUNCA incluye features nuevas ni cambios no relacionados con el bug.
