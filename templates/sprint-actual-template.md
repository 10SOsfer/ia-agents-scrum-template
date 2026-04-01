# Sprint [N] — [Fecha inicio] a [Fecha fin]

## Sprint Goal
[objetivo claro y medible]

## Historias seleccionadas
| ID | Título | Asignado a | Estado |
|----|--------|------------|--------|
| HU-X-Y | ... | OsferDevFront / OsferDevBack | Pendiente |

## Contratos de interfaz (COMPLETOS antes de iniciar desarrollo)

### Tipos compartidos
[Definir en pseudo-TypeScript TODAS las interfaces que comparten Frontend y Backend]
[OsferDevBack las traduce a código en ./output/src/types/]

```typescript
// Ejemplo de tipo compartido
interface User {
  id: string
  email: string
  name: string
  role: 'admin' | 'user'
  createdAt: string   // ISO 8601
}

interface PaginatedResponse<T> {
  success: true
  data: T[]
  meta: { cursor: string | null, limit: number, total: number }
}

interface ErrorResponse {
  success: false
  error: { code: string, message: string, details?: Array<{ field: string, message: string }> }
}
```

### Endpoints
[Usar el formato estándar de contratos definido en el CLAUDE.md del orquestador para cada endpoint]

## Datos de prueba
[OsferDevBack genera seed data específica del sprint en ./output/seeds/]
[OsferQA extiende con fixtures de casos borde en ./output/tests/fixtures/]

## Ownership de archivos
[Adaptar las rutas según el patrón de arquitectura elegido en architecture.md]

| Patrón | OsferDevFront | OsferDevBack |
|--------|--------------|--------------|
| Layered | components/, pages/, hooks/ | routes/, services/, models/, types/ |
| Clean Architecture | presentation/, application/hooks/ | domain/, application/use-cases/, infrastructure/, types/ |
| Hexagonal | adapters/ui/, pages/ | core/, adapters/in/, adapters/out/, types/ |

**Regla**: `./output/src/types/` es SIEMPRE propiedad exclusiva de OsferDevBack, sin importar el patrón.

## Reportes de desarrollo
[Los devs escriben aquí al completar]

## Reporte de revisión cruzada
[Resultado de la revisión Frontend <-> Backend]

## Reporte QA
[OsferQA escribe aquí]

## Registro de re-trabajo
| Ciclo | Historia | Problema | Dev asignado | Resultado |
|-------|----------|----------|--------------|-----------|
| 1 | HU-X-Y | [qué falló] | OsferDevFront/Back | Resuelto/Pendiente |

## Reporte de Deploy
[OsferOps escribe aquí]

## Sprint Review — Feedback del usuario
[OsferPO registra aquí el feedback del usuario tras la demo]

## Métricas del sprint (para retrospectiva)
- Historias planeadas: [N]
- Historias completadas: [N]
- Historias rechazadas por QA: [N] — motivos: [categorías]
- Ciclos de re-trabajo totales: [N]
- Bugs encontrados por severidad: Crítico [N], Alto [N], Medio [N], Bajo [N]
