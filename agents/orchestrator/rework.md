# Protocolo de re-trabajo (cuando QA rechaza historias)

Cuando OsferQA bloquea una o más historias:

1. **Las historias aprobadas NO se bloquean** — pueden avanzar a deploy independientemente si son funcionalmente independientes (OsferOps soporta deploy parcial)
2. OsferPO identifica qué dev debe corregir cada historia (OsferDevFront o OsferDevBack) según el reporte de QA
3. El dev corrige **solo lo reportado por QA** — no reescribir ni refactorizar código no relacionado
4. **Si el fix afecta la integración** (cambia un endpoint, un tipo, o un contrato) → se repite la revisión cruzada (paso 5 de Fase 2)
5. **Si el fix es aislado** (bug interno, lógica, estilos) → va directo a re-test de QA
6. OsferQA re-revisa **solo las historias corregidas**, no todo el sprint completo
7. **Máximo 2 ciclos** de fix → re-test por historia. Si después de 2 ciclos la historia sigue fallando:
   - Escalar al usuario usando el **formato de escalación** (ver abajo)
   - El usuario decide: un ciclo más, redefinir la historia, o moverla al siguiente sprint
8. Registrar cada ciclo de re-trabajo en sprint-actual.md para la retrospectiva

---

## Formato de escalación al usuario

Cuando OsferPO escala una historia bloqueada al usuario, DEBE incluir:

```
## ESCALACIÓN — HU-X-Y: [Título]
**Ciclos de re-trabajo completados**: 2 de 2 (máximo alcanzado)
**Agente responsable**: OsferDevFront / OsferDevBack

### Qué falla
[Descripción clara del problema — qué criterio de aceptación no se cumple]

### Qué se intentó
- **Ciclo 1**: [qué se cambió] → [resultado: qué siguió fallando]
- **Ciclo 2**: [qué se cambió] → [resultado: por qué no se resolvió]

### Causa raíz identificada
[Por qué sigue fallando después de 2 intentos — ej: ambigüedad en el requisito,
limitación técnica del stack, dependencia externa no prevista, etc.]

### Opciones para el usuario
- **A) Un ciclo más**: [qué se haría diferente esta vez y por qué podría funcionar]
- **B) Redefinir la historia**: [qué cambiar en los criterios de aceptación para hacerla viable]
- **C) Mover al siguiente sprint**: [impacto en otras historias que dependen de esta]
```

---

## Protocolo de timeboxing de cierre de sprint

Cuando QA encuentra bugs críticos o altos al final del sprint y el tiempo se agota:

1. **Evaluar la situación**: ¿cuántas historias están bloqueadas? ¿cuál es la severidad?
2. **Opciones (presentar al usuario para que decida):**
   - **Opción A — De-scope**: sacar las historias bloqueadas del sprint, desplegar las aprobadas (deploy parcial), mover las bloqueadas al siguiente sprint
   - **Opción B — Extensión controlada**: extender el sprint **máximo 2 días** para resolver los bugs. Si al cabo de 2 días siguen sin resolverse, aplicar Opción A automáticamente
   - **Opción C — Sprint fallido**: declarar el sprint fallido, no desplegar nada, llevar todo al siguiente sprint con prioridad máxima
3. **Registrar la decisión** en `./backlog/sprint-actual.md` con la justificación
4. **En la retrospectiva**: analizar por qué se llegó a esta situación y definir acciones preventivas

**Regla**: el sprint NUNCA se extiende indefinidamente. Máximo 2 días de gracia, y solo con aprobación del usuario.
