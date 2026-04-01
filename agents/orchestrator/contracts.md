# Contratos, tipos y templates

---

## Formato estándar de contratos

Los contratos en sprint-actual.md DEBEN seguir este formato para eliminar ambigüedades.
Usar pseudo-TypeScript para tipos y JSON para ejemplos:

```markdown
### Endpoint: [MÉTODO] [ruta]
**Historia**: HU-X-Y
**Autenticación**: Sí (Bearer token) / No

**Request:**
```typescript
// Body (solo POST/PATCH/PUT)
{
  field1: string        // descripción del campo
  field2: number        // descripción del campo
  field3?: string       // campo opcional
}

// Query params (si aplica)
?cursor=string&limit=number
```

**Response 200/201:**
```typescript
{
  success: true
  data: {
    id: string
    field1: string
    createdAt: string   // ISO 8601
  }
  meta?: { cursor: string, limit: number, total: number }
}
```

**Errores:**
| Código | Condición | Response body |
|--------|-----------|---------------|
| 400 | Validación fallida | `{ success: false, error: { code: "VALIDATION_ERROR", message: "...", details: [...] } }` |
| 401 | Sin token / token expirado | `{ success: false, error: { code: "UNAUTHORIZED", message: "..." } }` |
| 404 | Recurso no existe | `{ success: false, error: { code: "NOT_FOUND", message: "..." } }` |
```

---

## Gestión de tipos compartidos

- Los tipos compartidos en `./output/src/types/` son propiedad EXCLUSIVA de OsferDevBack
- OsferDevFront LEE los tipos pero NUNCA los modifica directamente
- Los contratos se definen COMPLETOS en sprint-actual.md ANTES de lanzar a los devs
- Si durante el desarrollo surge la necesidad de un tipo no previsto:
  - El dev que lo detecta lo documenta en sprint-actual.md
  - OsferPO evalúa si es un gap del contrato y decide cómo resolverlo

---

## Template de historia de usuario

```
Como [tipo de usuario]
Quiero [funcionalidad]
Para [valor de negocio]

Criterios de aceptación:
- [ ] CA1: [condición verificable]
- [ ] CA2: [condición verificable]
- [ ] CA3: [condición verificable]

Definition of Done:
- [ ] Código completo y testeado
- [ ] Documentación actualizada
- [ ] Criterios de aceptación verificados por OsferQA
- [ ] Revisión cruzada completada (OsferDevFront <-> OsferDevBack)
- [ ] Deploy exitoso en staging
- [ ] Demo al usuario en Sprint Review
```

---

## Ownership de archivos por patrón de arquitectura

| Patrón | OsferDevFront | OsferDevBack |
|--------|--------------|--------------|
| Layered | components/, pages/, hooks/ | routes/, services/, models/, types/ |
| Clean Architecture | presentation/, application/hooks/ | domain/, application/use-cases/, infrastructure/, types/ |
| Hexagonal | adapters/ui/, pages/ | core/, adapters/in/, adapters/out/, types/ |

**Regla**: `./output/src/types/` es SIEMPRE propiedad exclusiva de OsferDevBack, sin importar el patrón.

---

## Ceremonias Scrum

### Sprint Planning
- Definir el sprint goal
- Seleccionar historias del backlog priorizado
- Definir contratos completos (endpoints, tipos) usando el formato estándar
- Confirmar capacidad del equipo
- Registrar en `./backlog/sprint-actual.md`

### Sprint Review
- Presentar incremento funcional al usuario
- Demostrar historias contra criterios de aceptación
- Recoger feedback → registrar en sprint-actual.md
- Actualizar backlog según feedback recibido

### Sprint Retrospective
- Revisar métricas objetivas del sprint (velocity, rechazos QA, ciclos de re-trabajo)
- ¿Qué salió bien? ¿Qué se puede mejorar? ¿Qué acciones concretas tomamos?
- Registrar en `./backlog/retro.md` usando `./templates/retro-template.md`

### Backlog Refinement (entre sprints)
- Incorporar aprendizajes de la retrospectiva
- Re-priorizar historias según feedback del Sprint Review
- Refinar historias del próximo sprint para que cumplan INVEST
- Identificar dependencias técnicas nuevas
- Resolver ambigüedades antes de asignar
