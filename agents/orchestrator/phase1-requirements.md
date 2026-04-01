# Fase 1 — Requerimientos (detalle)

**OsferPO conduce esta fase directamente, actuando como cada agente en turno.**
Esto es necesario porque OsferBA y OsferArq requieren interacción con el usuario,
y los sub-agentes lanzados con Task() no pueden interactuar directamente.

---

## Paso 1 — Actuar como OsferBA (levantamiento de requisitos)
1. Leer `./agents/business-analyst/CLAUDE.md` como guía de comportamiento
2. Seguir el proceso definido allí: entender dominio → análisis estratégico → levantamiento con enriquecimiento → épicas → historias
3. **Aplicar el Patrón de Propuesta Enriquecida**: por cada requisito del usuario, proponer versión expandida con mejores prácticas
4. Interactuar con el usuario hasta tener todos los requisitos claros
5. Producir `./backlog/epics.md` con la señal de completitud de OsferBA

---

## Paso 2 — Recoger preferencias UX + Actuar como OsferArq + Lanzar OsferUX

### Sub-paso 2a — Recoger preferencias visuales del usuario (ANTES de lanzar OsferUX)

OsferPO pregunta al usuario sus preferencias de diseño para pasarlas como input a OsferUX.
**No lanzar OsferUX hasta tener estas respuestas.**

```
Antes de que nuestro diseñador empiece a trabajar, necesito conocer tu visión
para la interfaz. Responde lo que puedas:

1. ¿Qué tono quieres para el producto?
   - A) Profesional/serio (ej: Notion, Linear, Stripe)
   - B) Amigable/casual (ej: Slack, Duolingo, Todoist)
   - C) Minimalista/limpio (ej: Apple, Vercel)
   - D) Expresivo/colorido (ej: Figma, Canva)
   - E) Otro — descríbelo

2. ¿Hay algún producto o sitio web cuyo diseño te guste como referencia?
   (puede ser de cualquier industria)

3. ¿Tienes colores de marca o preferencias de color?
   (ej: "azul corporativo", "tonos cálidos", "sin preferencia")

4. ¿Tu público es técnico, no técnico, o mixto?

5. ¿Qué prioridad tiene el diseño visual vs la simplicidad funcional?
   - A) Que se vea moderno y atractivo aunque tenga más complejidad visual
   - B) Ultra simple y funcional aunque se vea básico
   - C) Balance entre ambos
```

Registrar las respuestas del usuario como **"Brief de diseño"** para incluirlas en el prompt de OsferUX.

### Sub-paso 2b — Lanzar OsferUX + Actuar como OsferArq (en paralelo)

Lanzar OsferUX como Task() autónomo **con las preferencias del usuario incluidas en el prompt**.
Mientras OsferUX trabaja de forma autónoma, OsferPO actúa como OsferArq interactuando con el usuario.

**OsferUX (autónomo, en paralelo):**
- Lanzar con Task() incluyendo el brief de diseño del usuario en el prompt
- Lee `./backlog/epics.md` + las preferencias del usuario y produce `./backlog/ux-flows.md`
- Trabaja sin bloquear al usuario ni esperar architecture.md

**OsferPO como OsferArq (con el usuario):**
1. Leer `./agents/architect/CLAUDE.md` como guía de comportamiento
2. Seguir el flujo de 7 pasos definido allí: leer epics.md → lineamientos técnicos → arquitectura → stack → BD → infra → architecture.md
3. **Presentar 3 opciones por decisión** y esperar que el usuario elija
4. **Una decisión a la vez** — no avanzar sin confirmación
5. Producir `./backlog/architecture.md` con la señal de completitud de OsferArq

---

## Paso 3 — Validar UX con el usuario + Compatibilidad UX ↔ Arquitectura + Consolidar stories.md

1. ESPERAR señal de completitud de OsferUX (si no terminó durante el paso 2)
2. **Presentar resumen de UX al usuario para validación**:
   - Mostrar la dirección estética y tono elegidos por OsferUX
   - Mostrar la arquitectura de información (estructura de navegación)
   - Mostrar 2-3 wireframes clave (pantalla principal, formulario más importante, dashboard si aplica)
   - Preguntar: "¿Esta dirección de diseño te parece correcta? ¿Quieres ajustar algo antes de continuar?"
   - Si el usuario pide cambios → re-lanzar OsferUX con Task() pasando: lo aprobado (preservar), lo rechazado (cambiar), y las instrucciones del usuario
   - **No continuar sin aprobación explícita del usuario sobre la dirección UX**
3. **Validar compatibilidad UX ↔ Arquitectura** antes de consolidar:
   - Revisar `ux-flows.md` contra `architecture.md` buscando conflictos:
     - ¿Los wireframes asumen SPA pero architecture.md eligió SSR (o viceversa)?
     - ¿Los flujos requieren tiempo real (WebSocket) pero solo se eligió REST?
     - ¿Los diseños asumen funcionalidades que el stack elegido no soporta?
   - Si hay conflictos → resolverlos con el usuario ANTES de consolidar stories.md
   - Documentar cualquier ajuste en `ux-flows.md` o `architecture.md`
4. Extraer las Historias de Usuario (HUs) de `./backlog/epics.md` (output de OsferBA)
5. Enriquecer cada HU con contexto de usabilidad de `./backlog/ux-flows.md` (output de OsferUX)
6. Agregar contexto técnico y restricciones de `./backlog/architecture.md` (output de OsferArq)
7. Priorizar usando los frameworks MoSCoW + Valor vs Esfuerzo combinados
8. Asegurar que cada historia cumple el criterio INVEST
9. Producir `./backlog/stories.md`

---

## Paso 4 — Validación del usuario
- Presentar el backlog priorizado al usuario
- **No avanzar a Fase 1.5 sin validación explícita del usuario**
