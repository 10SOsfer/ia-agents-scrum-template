# Fase 1.5 — Sprint 0 (Bootstrap del proyecto)

**Antes de que los devs escriban una línea de código, el entorno debe estar listo.**

---

## Paso 1 — OsferOps configura la infraestructura base
Lanzar OsferOps con Task() para:
1. Crear `./output/.gitignore` según el stack definido en architecture.md
2. Crear Dockerfile y docker-compose.yml adaptados al stack elegido
3. Crear pipeline CI/CD base (lint + test + build)
4. Crear archivos `.env.example` con todas las variables necesarias
5. Crear la estructura de carpetas según el patrón de arquitectura elegido

---

## Paso 2 — OsferDevBack y OsferDevFront inicializan sus proyectos (en paralelo)

**OsferDevBack** — Lanzar con Task():
1. Inicializar el proyecto backend (package.json, go.mod, pom.xml, etc. según stack)
2. Instalar dependencias base definidas en architecture.md
3. Crear `app.config` con validación de variables de entorno
4. Crear el logger (consola + archivo) según estándares del template
5. Crear endpoints base: `GET /health` y `GET /ready`
6. Crear endpoint `POST /api/logs` para recibir logs del frontend
7. Crear schema inicial de base de datos y primera migración
8. Crear seed data base para desarrollo y testing (usuarios demo, datos mínimos)
9. Verificar que `docker-compose up` arranca sin errores (docker-compose.yml lo crea OsferOps)

**OsferDevFront** — Lanzar con Task() en paralelo:
1. Inicializar el proyecto frontend (create-next-app, create-vite, angular-cli, etc. según stack)
2. Instalar dependencias base definidas en architecture.md (framework UI, state management, etc.)
3. Crear `app.config` del frontend con validación de variables de entorno
4. Crear el logger del frontend (consola + envío remoto a POST /api/logs)
5. Crear la estructura de componentes base según el patrón de arquitectura elegido
6. Configurar design tokens iniciales (colores, tipografía, espaciado) desde `ux-flows.md`
7. Crear layout principal y página de bienvenida/placeholder
8. Configurar el interceptor HTTP con Correlation ID
9. Verificar que el proyecto frontend arranca sin errores

**Nota**: Si la arquitectura es fullstack unificada (ej: Next.js con API routes), OsferDevBack inicializa el proyecto completo y OsferDevFront configura la capa de presentación dentro del mismo proyecto.

---

## Paso 3 — Validación del Sprint 0
OsferPO verifica el siguiente checklist antes de avanzar a Fase 2:

```
## Checklist de Sprint 0

### Infraestructura (OsferOps)
[ ] Estructura de carpetas creada según architecture.md
[ ] Dockerfile y docker-compose.yml funcionales
[ ] Pipeline CI/CD base configurado (lint + test + build)
[ ] .env.example con todas las variables documentadas
[ ] .gitignore configurado para el stack

### Backend (OsferDevBack)
[ ] Proyecto backend inicializado con dependencias base instaladas
[ ] app.config backend con validación de variables de entorno
[ ] Logger configurado (consola + archivo con rotación)
[ ] GET /health responde 200
[ ] GET /ready responde 200
[ ] POST /api/logs recibe y almacena logs correctamente
[ ] Schema de BD creado y primera migración ejecutada
[ ] Seed data base generada para desarrollo y testing

### Frontend (OsferDevFront)
[ ] Proyecto frontend inicializado con dependencias base instaladas
[ ] app.config frontend con validación de variables de entorno
[ ] Logger frontend configurado (consola + envío remoto)
[ ] Estructura de componentes base creada según patrón de arquitectura
[ ] Design tokens iniciales configurados desde ux-flows.md
[ ] Interceptor HTTP con Correlation ID configurado
[ ] Proyecto frontend arranca sin errores

### Integración
[ ] docker-compose up arranca backend + BD sin errores
[ ] Pipeline CI pasa en verde
```

**Señales de completitud requeridas:**
- `<!-- SEÑAL:COMPLETADO|OsferOps|Sprint0|[fecha-hora] -->`
- `<!-- SEÑAL:COMPLETADO|OsferDevBack|Sprint0|[fecha-hora] -->`
- `<!-- SEÑAL:COMPLETADO|OsferDevFront|Sprint0|[fecha-hora] -->`

**No avanzar a Fase 2 sin que TODOS los items del checklist estén verificados.**
