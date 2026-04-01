# Fase 2 — Ejecución por sprints (detalle)

Por cada sprint, seguir estos 10 pasos en orden:

---

## 1. Sprint Planning
- Seleccionar historias de `./backlog/stories.md`
- Crear `./backlog/sprint-actual.md` usando `./templates/sprint-actual-template.md`
- Definir el Sprint Goal

## 2. Definir contratos completos
**ANTES de lanzar a los devs, los contratos deben estar 100% definidos.**
Leer `./agents/orchestrator/contracts.md` para el formato estándar.

**Ownership de contratos:**
- **OsferPO (aplicando su conocimiento como OsferArq)** diseña los contratos usando `architecture.md` como referencia
- **OsferDevBack valida los contratos ANTES del desarrollo** — lanzar un Task() de validación separado:
  - DevBack lee los contratos en sprint-actual.md y verifica: ¿los tipos son consistentes? ¿los endpoints son implementables con el stack elegido? ¿faltan casos de error?
  - Si detecta inconsistencias → reporta a OsferPO, se ajustan los contratos, y se re-validan
  - **Solo cuando DevBack confirma "contratos validados"** se pasa al paso 3 (desarrollo)
- Una vez validados, los contratos son la fuente de verdad para ambos devs

**Contenido obligatorio:**
- Endpoints: método, ruta, request body (con tipos), response body (con tipos), códigos de error
- Tipos compartidos: todas las interfaces que Frontend y Backend comparten, escritas en pseudo-TypeScript
- OsferDevBack generará estos tipos en `./output/src/types/` como primer paso de su trabajo
- **Si un contrato no está definido aquí, no se puede implementar**

## 3. Desarrollo en paralelo
Lanzar OsferDevFront y OsferDevBack en paralelo con Task():
- **OsferDevBack genera los tipos compartidos en `./output/src/types/` PRIMERO** y escribe la señal `<!-- SEÑAL:TIPOS_LISTOS|OsferDevBack|./output/src/types/|[fecha-hora] -->` en sprint-actual.md
- OsferDevFront: arranca leyendo wireframes y creando estructura mientras tanto, usa mocks temporales hasta que la señal TIPOS_LISTOS aparezca en sprint-actual.md
- Ownership exclusivo de archivos (sin solapamiento)
- OsferDevBack es dueño de `./output/src/types/` — OsferDevFront los lee pero NUNCA los modifica

## 4. Esperar completitud
ESPERAR señales de completitud de ambos devs.

## 5. Revisión cruzada
Antes de QA, se ejecuta esta revisión obligatoria.

**Modelo de ejecución**: OsferPO lanza **dos Task() en paralelo**, cada uno con prompt de revisión (no de desarrollo).
Las rutas a revisar dependen del patrón de arquitectura elegido — consultar `architecture.md` para determinar la estructura de carpetas real:
- **Task() OsferDevFront (modo revisión)**: lee el código del backend (endpoints y tipos compartidos), compara contra los contratos en sprint-actual.md, y reporta: ¿los endpoints devuelven lo que espero? ¿los formatos son correctos? ¿faltan campos?
- **Task() OsferDevBack (modo revisión)**: lee el código del frontend (llamadas a API y hooks/servicios que consumen endpoints), y reporta: ¿Frontend consume los endpoints correctamente? ¿maneja los errores? ¿envía los tipos correctos?

**Dónde buscar según el patrón de arquitectura:**
| Patrón | Endpoints del backend | Llamadas a API del frontend |
|--------|----------------------|----------------------------|
| Layered | `routes/`, `controllers/` | `services/`, `hooks/` |
| Clean Architecture | `infrastructure/http/` | `infrastructure/api/`, `application/hooks/` |
| Hexagonal | `adapters/in/` | `adapters/api/`, `core/ports/` |

**Cada Task() produce** un reporte en sprint-actual.md sección "Reporte de revisión cruzada" con:
- Lista de endpoints verificados: OK / DISCREPANCIA
- Detalle de cada discrepancia encontrada
- Si hay discrepancias → OsferPO asigna correcciones al dev correspondiente ANTES de pasar a QA

## 6. Quality Assurance
Lanzar OsferQA con Task() sobre lo producido.
- Si OsferQA aprueba todas las historias → continuar al paso 7
- Si OsferQA reporta fallos → leer `./agents/orchestrator/rework.md` y seguir el protocolo

## 7. Deploy
Lanzar OsferOps para integrar y hacer deploy a staging.

## 8. Sprint Review (demo al usuario)
- Presentar el incremento funcional al usuario
- Demostrar las historias completadas contra los criterios de aceptación
- Recoger feedback del usuario
- Si hay cambios solicitados → registrar como nuevas historias o refinamiento

## 9. Retrospectiva + Refinamiento
- **Retrospectiva con métricas objetivas**:
  - Historias planeadas vs completadas (velocity)
  - Historias rechazadas por QA y motivos (por categoría: bugs, integración, criterios)
  - Ciclos de re-trabajo por historia (idealmente 0-1)
  - ¿Qué salió bien? ¿Qué mejorar? ¿Acciones concretas?
  - Registrar en `./backlog/retro.md` usando `./templates/retro-template.md`
- **Refinamiento del backlog**: incorporar aprendizajes de la retro, re-priorizar historias pendientes, refinar historias del próximo sprint para que cumplan INVEST
- Actualizar `./backlog/stories.md` con los cambios y registrar en el historial

## 10. Archivar sprint y comenzar el siguiente
1. Renombrar `./backlog/sprint-actual.md` a `./backlog/sprint-[N].md` (ej: `sprint-1.md`)
2. Crear nuevo `./backlog/sprint-actual.md` usando el template para el siguiente sprint
3. Volver al paso 1 con las historias refinadas

---

## Gestión de dependencias entre agentes
Cuando dos agentes trabajan en paralelo, antes de lanzarlos:
1. Definir qué archivos/carpetas le pertenecen exclusivamente a cada uno
2. Definir contratos COMPLETOS en sprint-actual.md usando el formato estándar
3. Si un agente está bloqueado por otro → escalar al usuario inmediatamente

## Protocolo de propagación de cambios
Cuando OsferArq cambia una decisión arquitectónica vía `cambiar-decision`:
1. OsferPO (actuando como OsferArq) actualiza `./backlog/architecture.md`
2. OsferPO evalúa qué agentes están impactados por el cambio
3. OsferPO re-lanza los agentes afectados con contexto explícito de qué cambió y por qué
4. Si hay trabajo en progreso afectado, OsferPO notifica al usuario antes de actuar

## Historial de cambios del backlog
Cuando se refinan historias o cambian prioridades, OsferPO registra el cambio en `./backlog/stories.md`:
```markdown
## Historial de cambios del backlog
| Fecha | Cambio | Razón | Impacto |
|-------|--------|-------|---------|
| YYYY-MM-DD | Descripción del cambio | Por qué se hizo | Qué historias/sprints afecta |
```
