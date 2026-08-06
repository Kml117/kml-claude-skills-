# Recursos de Aprendizaje — Cursos, Libros y Benchmarks

## Tabla de contenidos
1. [Formación en español](#formación-en-español)
2. [Libros fundamentales](#libros-fundamentales)
3. [Benchmarks de IA para código](#benchmarks)
4. [Ruta de aprendizaje recomendada](#ruta-de-aprendizaje)

---

## Formación en español

### JSCamp InfoJobs (midudev) — GRATIS
- **Instructor**: Miguel Ángel Durán (Google Developer Expert, GitHub Star)
- **Plataforma**: jscamp.dev + github.com/midudev/jscamp
- **Costo**: Acceso libre
- **Cubre** (progresivo):
  - HTML/CSS desde cero
  - JavaScript y programación funcional
  - React (componentes, hooks, estado)
  - TypeScript (tipado estático)
  - Node.js backend
  - Docker y containerización
  - CI/CD pipelines
  - APIs de IA y MCP (Model Context Protocol)
  - Streaming en tiempo real
  - Variables de entorno y rate limiting

**Por qué es excelente**: Bootcamp completo y gratuito que lleva de cero
a fullstack con IA integrada. El repositorio público permite seguir a
ritmo propio.

### DevTalles (Fernando Herrera)
- **Plataforma**: devtalles.com
- **Costo**: Suscripción de pago
- **Cubre**:
  - Arquitectura limpia
  - Docker avanzado
  - Diseño orientado a objetos
  - Claude Code de Anthropic (guía práctica)
  - Frontend y backend con TypeScript

**Por qué es excelente**: Complemento ideal a JSCamp para profundizar
en arquitectura y patrones avanzados. La guía de Claude Code es única
en el ecosistema hispanohablante.

### Platzi
- **Fundamentos de Arquitectura de Software**: 24 clases, 4.8/5
  URL: platzi.com/cursos/fundamentos-arquitectura-software/
- **Profesional de Arquitectura de Software**: 43 clases, avanzado
  URL: platzi.com/cursos/pro-arquitectura/
- **Costo**: Suscripción (~$49/mes)

---

## Libros fundamentales

### 1. Código Limpio (Robert C. Martin)
- **ISBN 1a edición (español)**: 978-84-415-3210-6 (Anaya Multimedia, 2012)
- **ISBN 2a edición (español)**: 978-84-415-5301-9
- **Nivel**: Principiante → Intermedio
- **Novedades 2a edición**: Integración de IA generativa en workflows,
  aplicación de SOLID en JS, Go, Python, Clojure, C, C#
- **Conceptos clave**:
  - Nombres que revelan intención
  - Funciones pequeñas de propósito único
  - Separación de queries y comandos
  - Error handling en bloques aislados
  - Tests como documentación viva

### 2. Fundamentals of Software Architecture (Richards & Ford)
- **Español**: "Arquitectura. Fundamentos del software"
- **Nivel**: Principiante → Intermedio
- **Cubre**: Atributos de calidad, estilos arquitectónicos,
  monolito vs microservicios, responsabilidades del arquitecto

### 3. Clean Architecture (Robert C. Martin)
- **Español**: "Arquitectura limpia" (Anaya Multimedia)
- **Nivel**: Intermedio
- **Cubre**: SOLID, desacoplamiento de capas, frameworks como detalles

---

## Benchmarks

Los benchmarks evalúan formalmente qué tan bien los modelos de IA
resuelven problemas reales de programación. Entenderlos es esencial
para calibrar cuánto confiar en el código generado por IA.

### SWE-bench (ICLR 2024)
- **Qué es**: Bugs reales de repos Python open source (Django, Flask, SymPy)
- **Tarea**: El modelo debe proponer un parche funcional autónomamente
- **Problema**: 32.67% de resoluciones tenían fugas de solución en
  comentarios; 31.08% tenía tests de validación demasiado débiles
- **Relevancia**: Mostró que los benchmarks iniciales sobreestimaban
  la capacidad real de los modelos

### SWE-bench Verified
- **Qué es**: Filtro de 500 instancias verificadas por 93 devs expertos
- **Garantía**: Tests robustos, correcciones viables para humanos expertos
- **Uso**: Estándar de la industria para medir asistentes de código

### SWE-bench Pro
- **Qué es**: Tareas enterprise-level con repos GPL/comerciales
- **Requisito**: Reestructuración de múltiples archivos (no trivial)
- **Resultado**: Modelos actuales < 25% de éxito
- **Lección**: La IA no reemplaza al desarrollador en tareas complejas

### SWE-Bench++ (2025)
- **Qué es**: Evaluación multilingüe (11 lenguajes), 1,782 instancias
- **Resultados actuales**:
  - Claude Sonnet 4.5: 36.20% (pass@10)
  - GPT-5: 34.57%
  - Gemini 2.5 Pro: 24.92%
  - GPT-4o: 16.89%
- **Lección fundamental**: Incluso el mejor modelo resuelve solo ~1/3
  de problemas reales. La supervisión humana es irremplazable.

---

## Ruta de aprendizaje

### Fase 0 — Fundamentos (Semanas 1-4)
**Objetivo**: HTML, CSS, JavaScript básico
- JSCamp (primeros módulos)
- Entender el DOM, eventos, fetch API
- **Entregable**: Página web estática responsive

### Fase 1 — TypeScript y React (Semanas 5-8)
**Objetivo**: Tipado estático + componentes
- JSCamp (módulos de TS y React)
- Hooks: useState, useEffect, useRef
- **Entregable**: SPA con React y TypeScript

### Fase 2 — Next.js Fullstack (Semanas 9-14)
**Objetivo**: SSR, API Routes, Server Actions
- Server Components vs Client Components
- App Router, layouts, routing dinámico
- Conectar a PostgreSQL con Prisma o Drizzle
- **Entregable**: App fullstack con auth y DB

### Fase 3 — Seguridad y Testing (Semanas 15-18)
**Objetivo**: Auth, CAPTCHA, tests
- Implementar Clerk o Better Auth
- Turnstile + rate limiting + Zod
- Tests unitarios (Vitest) + E2E (Playwright)
- Clean Code: nombres, funciones, error handling
- **Entregable**: App con auth, tests y CI/CD

### Fase 4 — Producción y DevOps (Semanas 19-22)
**Objetivo**: Deploy, Docker, CI/CD
- Vercel deployment
- Docker para desarrollo local
- GitHub Actions pipeline
- Monitoreo y logging
- **Entregable**: App desplegada con pipeline completo

### Fase 5 — Especialización (Mes 6+)
Elegir camino según interés:
- **SaaS** → Activar skill saas-architect
- **AI/ML** → APIs de Claude, OpenAI, embeddings
- **Mobile** → React Native / Flutter
- **Data** → Python, pandas, SQL avanzado
