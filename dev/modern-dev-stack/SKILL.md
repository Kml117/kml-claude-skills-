---
name: modern-dev-stack
description: >
  Guía de arquitectura y stack para desarrollo de software moderno con IA. Usa SIEMPRE
  que el usuario mencione: stack de desarrollo, Next.js, React, Server Components,
  App Router, TypeScript, Prisma, Drizzle, ORM, PostgreSQL, Redis, SQLite, MySQL,
  Clerk, Better Auth, NextAuth, Auth.js, autenticación, SSO, Cloudflare Turnstile,
  CAPTCHA, Resend, correo transaccional, Vitest, Jest, Playwright, E2E, testing,
  pruebas unitarias, Clean Code, código limpio, SOLID, entorno de desarrollo, hardware
  para programar, SPA, JAMstack, SWE-bench, AI-assisted coding, configurar proyecto,
  setup de proyecto, boilerplate, monolito modular, microservicios, API REST, serverless,
  edge runtime, Vercel, middleware, rate limiting, Zod, validación, o cuando el usuario
  quiera elegir tecnologías para un proyecto web, configurar su entorno de desarrollo,
  integrar herramientas de IA en su flujo de trabajo, o necesite una guía para pasar de
  principiante a profesional en desarrollo fullstack moderno.
---

# Modern Dev Stack — Guía de Arquitectura Fullstack

## Propósito

Guiar la selección, configuración e integración del stack de desarrollo de software
moderno (2025-2026), con énfasis en el uso responsable de IA como copiloto.
Cubre desde la elección de hardware hasta testing E2E en producción.

## Principio rector: Confianza Progresiva en IA

El 84% de los desarrolladores usa herramientas de IA, pero solo el 29% confía en
la exactitud de los resultados (Stack Overflow 2025). El 66% se frustra con código
"casi correcto". Para el aprendiz, esto significa:

1. **Dominar fundamentos PRIMERO** — No se puede evaluar código generado sin
   entender bases de datos, HTTP, arquitectura
2. **Tratar la IA como ingeniero junior** — Requiere supervisión, validación
   manual y tests automatizados
3. **Delegar progresivamente** — Empezar con boilerplate y docs, nunca con
   lógica de negocio crítica sin revisión

## Flujo de trabajo

### 1. Diagnóstico del Proyecto

Antes de recomendar stack, recopilar:

```
┌─ DIAGNÓSTICO DE PROYECTO ──────────────────────────────┐
│ 0. ¿Cuál es la Complejidad? (Simple/Media/Compleja/     │
│    Enterprise) — ver references/notion-workflow-        │
│    integration.md para la taxonomía completa y qué      │
│    skills activar según el nivel                        │
│ 1. ¿Qué tipo de producto?                              │
│    Web app / API / Mobile / Desktop / CLI               │
│ 2. ¿Frontend necesario?                                 │
│    SÍ → Next.js (SSR/SSG)  |  NO → API pura            │
│ 3. ¿Base de datos requerida?                            │
│    Relacional (Postgres) | Cache (Redis) | Ambas        │
│ 4. ¿Autenticación de usuarios?                          │
│    Simple → Better Auth | Rápida → Clerk | Manual → No  │
│ 5. ¿Escala esperada?                                    │
│    MVP (<1K users) | Growth (<50K) | Scale (>50K)       │
│ 6. ¿Presupuesto mensual para servicios?                 │
│    $0 (free tiers) | <$100 | <$500 | Enterprise        │
│ 7. ¿Solo o con equipo?                                  │
│ 8. ¿El proyecto es SaaS multi-tenant?                   │
│    SÍ → Consultar skill saas-architect                  │
└─────────────────────────────────────────────────────────┘
```

Si el usuario no declara la Complejidad, pregúntala antes de seguir — es lo
que decide si este diagnóstico basta solo o si hay que traer `csdd-mentor`
y/o `saas-architect` a la conversación. No asumas Simple por defecto.

### 2. Selección de Stack

Árbol de decisión por perfil:

```
¿Necesita frontend?
├── SÍ → ¿SEO importante?
│        ├── SÍ → Next.js (App Router + Server Components)
│        └── NO → React SPA (Vite) o Next.js
├── NO → ¿Lenguaje preferido?
│        ├── TypeScript → Node.js/Bun + Hono/Express
│        ├── Python → Django / FastAPI
│        └── PHP → Laravel
└── AMBOS → Next.js fullstack (API Routes + Frontend)

¿Base de datos?
├── MVP simple → SQLite (cero config)
├── Multi-usuario/SaaS → PostgreSQL (RLS para tenancy)
├── Alto tráfico de lectura → PostgreSQL + Redis cache
└── Tiempo real → PostgreSQL + Redis Pub/Sub

¿ORM (TypeScript)?
├── Prioriza velocidad de dev → Prisma (schema-first, Studio)
├── Prioriza rendimiento serverless → Drizzle (12KB, cold start <10ms)
└── Proyecto serverless + edge → Drizzle (obligatorio)

¿Autenticación?
├── MVP rápido, < 50K MAU gratis → Clerk (todo incluido)
├── Control total de datos → Better Auth (PostgreSQL propio)
├── Ya tiene NextAuth v4 → Migrar a Auth.js v5
└── Enterprise SSO → SAML Jackson + Better Auth
```

### 3. Stack de Referencia por Perfil

| Perfil | Frontend | Backend | DB | ORM | Auth | Deploy |
|---|---|---|---|---|---|---|
| **MVP rápido** | Next.js | API Routes | PostgreSQL | Prisma | Clerk | Vercel |
| **Fullstack pro** | Next.js | API Routes | PostgreSQL | Drizzle | Better Auth | Vercel |
| **API pura** | — | Hono/Express | PostgreSQL | Drizzle | Better Auth | Railway |
| **SaaS B2B** | Next.js | API Routes | PostgreSQL + Redis | Prisma | Better Auth + SAML | Vercel + AWS |
| **Estudiante** | Next.js | API Routes | SQLite → Postgres | Prisma | Clerk free | Vercel free |

Para detalles de cada componente, consultar los archivos de referencia.

### 4. Checklist de Proyecto Nuevo

```
□ Git repo inicializado con .gitignore apropiado
□ TypeScript strict mode habilitado
□ Variables de entorno en .env.local (nunca en código)
□ Esquema de DB definido con migraciones (NO db push en prod)
□ Índices explícitos en foreign keys del ORM
□ Auth configurado con validación en server-side (no solo middleware)
□ Turnstile/CAPTCHA en formularios públicos
□ Rate limiting por IP (Upstash Redis o Vercel KV)
□ Zod para validación de inputs en server actions
□ Tests unitarios (Vitest) para lógica de negocio
□ Test E2E (Playwright) para flujos críticos
□ CI/CD pipeline (GitHub Actions → Vercel)
□ Resend configurado con DKIM + SPF + return-path
□ Error handling con bloques try/catch aislados
□ Nombres descriptivos, funciones pequeñas, SRP
```

### 5. Anti-patrones Críticos

Alertar inmediatamente si el usuario intenta:

1. **`db push` en producción**: Destruye datos. Usar migraciones siempre
   (`prisma migrate deploy` o `drizzle-kit migrate`).

2. **Auth solo en middleware edge**: Vulnerable a CVE-2025-29927 (bypass
   via x-middleware-subrequest). Validar TAMBIÉN en server-side.

3. **No indexar foreign keys**: Prisma y Drizzle NO crean índices automáticos
   en FKs. Los JOINs degradan exponencialmente sin índices.

4. **Confundir `relations()` de Drizzle con FK reales**: `relations()` son
   metadatos internos. Declarar `references` explícitamente para que la DB
   aplique restricciones de integridad.

5. **Confiar ciegamente en código de IA**: El 45.2% de devs reporta que
   debuggear código de IA toma más tiempo del esperado. Revisar siempre.

6. **CAPTCHA solo en frontend**: El token de Turnstile DEBE validarse en
   el backend contra el endpoint de Cloudflare. Sin validación server-side,
   un bot puede enviarlo vacío.

## Integración con CSDD

Si el proyecto usa Constitutional Spec-Driven Development:
- `constitution.md` debe incluir artículos sobre validación de inputs
  (CWE-20), autenticación segura (CWE-287), y manejo de errores (CWE-755)
- El spec de cada API endpoint documenta el esquema Zod de validación
- Tests E2E mapean directamente a los artículos de la constitución

## Integración con saas-architect

Si el diagnóstico revela que el proyecto es SaaS multi-tenant:
- Activar la skill `saas-architect` para modelos de tenencia y aislamiento
- Esta skill cubre el stack técnico; `saas-architect` cubre la arquitectura
  de negocio multi-tenant

## Integración con csdd-mentor y con Notion

Ver `references/notion-workflow-integration.md` para: la taxonomía completa
de Complejidad compartida entre las tres skills, cuándo NO forzar CSDD
(Simple/Media), los nombres exactos de las bases de Notion del usuario, y
cómo ofrecer registrar tareas/bugs/sesiones ahí si el MCP de Notion está
disponible.

## Archivos de referencia

Consultar según necesidad — NO cargar todos de golpe:

| Archivo | Cuándo leer |
|---|---|
| `references/frontend-architecture.md` | Al decidir frontend (SPA vs SSR vs SSG) |
| `references/database-and-orms.md` | Al elegir DB y ORM |
| `references/auth-and-security.md` | Al configurar autenticación y seguridad |
| `references/testing-and-quality.md` | Al estructurar tests y código limpio |
| `references/dev-environment.md` | Al configurar hardware y herramientas |
| `references/learning-resources.md` | Si el usuario pide cursos o libros |
| `references/notion-workflow-integration.md` | Al iniciar CUALQUIER proyecto nuevo — define complejidad y qué skills activar |

## Formato de salida

Cuando el usuario pida configurar un proyecto, producir:

1. **Tabla de stack**: Tecnología elegida por capa + justificación
2. **Diagrama de arquitectura**: Componentes y flujo de datos
3. **Comandos de setup**: Paso a paso para inicializar el proyecto
4. **Schema de DB inicial**: Con índices, tipos, relaciones
5. **Checklist de seguridad**: Estado de cada punto (✅/❌)
6. **Ruta de aprendizaje**: Si el usuario es principiante
