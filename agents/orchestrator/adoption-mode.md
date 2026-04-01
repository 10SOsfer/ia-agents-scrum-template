# Modo Adopción — Integrar equipo Scrum a un proyecto existente

Este modo se activa cuando el usuario dice "adoptar este proyecto", "integrar equipo scrum",
o cuando OsferPO detecta que ya hay código, repositorio o documentación existente.

**Principio fundamental**: El proyecto existente NO cambia su estructura de código.
La capa de gestión de la plantilla (`backlog/`, `agents/`, `templates/`) se monta encima.
El `./output/` apunta a donde ya vive el código.

---

## Fase 0 — Escaneo y reverse-engineering

### 0.1 — Escaneo de estructura

OsferPO escanea el proyecto para entender qué existe:

**Código:**
- Leer estructura de carpetas, package.json/go.mod/pom.xml, dependencias
- Identificar stack: lenguaje, framework backend, framework frontend, BD
- Identificar patrón de arquitectura (capas, clean, hexagonal, otro)
- Identificar si es monorepo, repos separados, fullstack unificado

**Repositorio (si existe):**
- Leer historial de git para entender evolución y contribuidores
- Identificar ramas activas, CI/CD existente, pipelines

**Documentación (si existe):**
- Buscar READMEs, docs/, wikis, historias de usuario, specs
- Buscar archivos de arquitectura, diagramas, ADRs

**Infraestructura:**
- Buscar Dockerfiles, docker-compose, manifiestos K8s
- Buscar pipelines CI/CD (.github/workflows, .gitlab-ci.yml, Jenkinsfile)
- Buscar archivos .env, configuraciones de deploy

### 0.2 — Auditoría técnica

OsferPO lanza en paralelo:
- **Task() OsferDevBack**: analizar código backend — ¿compila? ¿hay tests? ¿pasan? ¿endpoints rotos? ¿deuda técnica evidente?
- **Task() OsferDevFront**: analizar código frontend — ¿arranca? ¿hay tests? ¿componentes rotos? ¿estados incompletos?
- **Task() OsferQA**: ejecutar checklist de calidad — ¿cobertura de tests? ¿vulnerabilidades? ¿dependencias desactualizadas?

Cada agente produce un reporte clasificando cada hallazgo:

| Tag | Significado | Acción |
|-----|-------------|--------|
| `FUNCIONA` | Código correcto, sin problemas | No tocar |
| `FUNCIONA_CON_PROBLEMAS` | Funciona pero tiene issues menores | Historia tipo FIX (prioridad media) |
| `NO_FUNCIONA` | Roto, no compila, errores en runtime | Historia tipo FIX (prioridad alta) |
| `RIESGO_SEGURIDAD` | Vulnerabilidad, credenciales expuestas, inyección | Historia tipo FIX (prioridad crítica) |

### 0.3 — Generación de artefactos borrador

Con la información del escaneo, OsferPO genera borradores de los artefactos de la plantilla:

1. **`./backlog/epics.md` (borrador)** — Inferir épicas del código existente:
   - Cada módulo/funcionalidad detectada se convierte en una épica
   - Las historias existentes se marcan con tag `[EXISTENTE]`

2. **`./backlog/architecture.md` (borrador)** — Documentar el stack actual:
   - Stack detectado, patrón de arquitectura, BD, servicios externos
   - ADRs retroactivos para decisiones ya tomadas (inferidas del código)

3. **`./backlog/ux-flows.md` (borrador)** — Si hay frontend:
   - Inferir flujos de las pantallas/componentes existentes
   - Documentar sistema de diseño actual (colores, tipografía, componentes)

4. **Reporte de salud** — Consolidar hallazgos de la auditoría:
   - Resumen ejecutivo: X endpoints, Y componentes, Z tests
   - Clasificación: cuántos FUNCIONA / CON_PROBLEMAS / NO_FUNCIONA / RIESGO

---

## Fase 0.5 — Validación + Extensión (interactiva con el usuario)

**Esta fase es completamente interactiva.** OsferPO presenta cada artefacto borrador al usuario
y le da control total para corregir, modificar y extender.

### Paso 1 — Validar lo encontrado

Por cada artefacto borrador, presentar al usuario:

```
## Esto es lo que encontré en tu proyecto:

### Stack detectado
- Backend: [framework + lenguaje]
- Frontend: [framework + lenguaje]
- Base de datos: [motor]
- CI/CD: [herramienta] / No detectado

¿Es correcto? ¿Hay algo que no detecté o que está mal?
```

```
### Funcionalidades existentes
Encontré [N] módulos/funcionalidades:
1. [Módulo A] — [descripción inferida] — Estado: FUNCIONA
2. [Módulo B] — [descripción inferida] — Estado: FUNCIONA_CON_PROBLEMAS
3. [Módulo C] — [descripción inferida] — Estado: NO_FUNCIONA

¿Falta alguno? ¿Alguna descripción es incorrecta?
```

```
### Reporte de salud
- [N] endpoints: [X] funcionan, [Y] con problemas, [Z] rotos
- [N] componentes UI: [X] funcionan, [Y] con problemas
- Tests: [cobertura]% — [N] pasan, [M] fallan
- Dependencias desactualizadas: [lista]
- Riesgos de seguridad: [lista o "ninguno detectado"]

¿Quieres que arreglemos lo roto antes de agregar cosas nuevas,
o prefieres avanzar con lo nuevo y arreglar después?
```

### Paso 2 — Extender y modificar

Después de validar lo existente, preguntar al usuario qué quiere hacer:

```
Ahora que tenemos claro el estado del proyecto, ¿qué quieres hacer?

1. **Agregar funcionalidades nuevas** — Descríbeme qué quieres agregar
2. **Modificar funcionalidades existentes** — Dime qué quieres cambiar
3. **Rediseñar la interfaz** — Podemos cambiar el look & feel
4. **Cambiar arquitectura/stack** — Migrar tecnologías
5. **Solo arreglar lo roto** — Corregir bugs y deuda técnica
6. **Combinación** — Varias de las anteriores

¿Qué prefieres? (puedes elegir varias)
```

**Para funcionalidades nuevas**: Aplicar el mismo Patrón de Propuesta Enriquecida del BA.
**Para rediseño UX**: Ejecutar el sub-paso 2a de preferencias visuales (igual que en proyecto nuevo).
**Para cambios de arquitectura**: Usar el flujo de `cambiar-decision` del OsferArq.

### Paso 3 — Recoger preferencias UX (si aplica)

Si el usuario quiere rediseñar la interfaz o agregar pantallas nuevas,
ejecutar las mismas 5 preguntas de preferencias visuales del sub-paso 2a de la Fase 1
(ver `./agents/orchestrator/phase1-requirements.md`).

Si el usuario está conforme con el diseño actual, documentar:
"Brief de diseño: mantener el estilo visual actual del proyecto."

---

## Fase 0.7 — Consolidación de artefactos

OsferPO consolida todo en los artefactos definitivos:

### 1. Actualizar `./backlog/epics.md`
- Épicas existentes (inferidas del código) + épicas nuevas (del usuario)
- Cada épica marcada con su origen

### 2. Actualizar `./backlog/architecture.md`
- Stack actual documentado con ADRs retroactivos
- Cambios de arquitectura solicitados por el usuario (si aplica)

### 3. Producir `./backlog/stories.md`
Cada historia tiene un tag que indica su tipo:

| Tag | Significado | Qué hace el dev |
|-----|-------------|-----------------|
| `[EXISTENTE]` | Ya implementada y funciona | No se toca — solo se documenta |
| `[FIX]` | Existe pero está rota o tiene problemas | Corregir solo lo reportado |
| `[MODIFICACIÓN]` | Funciona pero el usuario quiere cambiarla | Modificar según nuevos criterios |
| `[NUEVA]` | No existe, hay que construirla desde cero | Implementar completa |

**Priorización sugerida:**
1. `[FIX]` con prioridad CRÍTICA (riesgos de seguridad) → Sprint 1
2. `[FIX]` con prioridad ALTA (cosas rotas) → Sprint 1
3. `[NUEVA]` Must Have → Sprint 1-2
4. `[MODIFICACIÓN]` → Sprint 2+
5. `[FIX]` con prioridad MEDIA (problemas menores) → cuando encaje

El usuario tiene la última palabra sobre la priorización.

### 4. Generar `./backlog/ux-flows.md` (si aplica)
- Si hay rediseño → lanzar OsferUX con brief del usuario + contexto del diseño actual
- Si no hay rediseño → documentar el diseño actual como referencia

### 5. Configurar `./output/`
- Si el código ya está en la carpeta del proyecto → configurar `./output/` como symlink o referencia a la ubicación real del código
- Si el código está en otra ubicación → documentar la ruta real en architecture.md
- **No mover ni copiar el código existente** — trabajar donde ya está

### 6. Validación final con el usuario
Presentar el backlog consolidado:
```
## Resumen del plan de trabajo

### Lo que ya funciona (no se toca): [N] historias [EXISTENTE]
### Lo que hay que arreglar: [N] historias [FIX]
### Lo que quieres cambiar: [N] historias [MODIFICACIÓN]
### Lo nuevo que quieres agregar: [N] historias [NUEVA]

### Sprint 1 propuesto:
[lista de historias priorizadas]

¿Apruebas este plan? ¿Quieres ajustar prioridades?
```

**No avanzar sin aprobación explícita del usuario.**

---

## Entrada al flujo normal

Después de la Fase 0.7, OsferPO determina desde dónde continuar:

| Situación del proyecto | Entrada |
|------------------------|---------|
| No tiene CI/CD ni Docker ni estructura base | Fase 1.5 (Sprint 0) — bootstrap completo |
| Tiene CI/CD pero falta estructura base | Fase 1.5 parcial — solo lo que falta |
| Tiene todo listo (CI, Docker, estructura, tests) | Fase 2 directamente — Sprint 1 con historias priorizadas |

A partir de aquí, el flujo es idéntico al de un proyecto nuevo (Fase 2 en adelante).

---

## Notas importantes del Modo Adopción

- **No reescribir código que funciona** — respetar lo existente
- **No cambiar estructura de carpetas existente** — adaptar la plantilla al proyecto, no al revés
- **No imponer stack** — documentar el stack que ya existe
- **Las historias `[EXISTENTE]` nunca entran a un sprint** — solo sirven como documentación
- **El usuario decide la prioridad** entre arreglar lo roto vs agregar lo nuevo
- **Si el proyecto no tiene tests**, el primer sprint debería incluir historias de tipo `[FIX]` para agregar tests a los flujos críticos existentes
