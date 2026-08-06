# Frameworks y Stacks de Referencia

## Tabla de contenidos
1. [Twelve-Factor App](#twelve-factor-app)
2. [AWS Well-Architected SaaS Lens](#aws-well-architected-saas-lens)
3. [AWS SaaS Journey Framework](#aws-saas-journey-framework)
4. [Combinación A: Enterprise Next.js Stack](#combinación-a-enterprise-nextjs-stack)
5. [Combinación B: Laravel Spark-Jetstream Stack](#combinación-b-laravel-spark-jetstream-stack)
6. [Combinación C: Django SaaS Stack](#combinación-c-django-saas-stack)
7. [Herramientas de soporte del Plano de Control](#herramientas-de-soporte)
8. [Mapa de relaciones](#mapa-de-relaciones)

---

## Twelve-Factor App

**Origen**: Ingenieros de Heroku (Adam Wiggins), 2011.
**URL**: https://12factor.net (disponible en español)

La base conceptual obligatoria. Todo código SaaS debe cumplir estos 12 factores:

| # | Factor | Implicación SaaS |
|---|---|---|
| I | Codebase | Un repo → N despliegues. NUNCA forks por tenant |
| II | Dependencies | Declarar explícitamente. Lock files obligatorios |
| III | Config | Variables de entorno. NUNCA credenciales en código |
| IV | Backing Services | DB, Redis, colas = recursos adjuntos intercambiables |
| V | Build/Release/Run | Separar fases. Cada release tiene ID único |
| VI | Processes | Stateless. Sin sesión en memoria local |
| VII | Port Binding | Exportar servicio vía puerto. Auto-contenido |
| VIII | Concurrency | Escalar horizontalmente con procesos independientes |
| IX | Disposability | Inicio rápido + graceful shutdown |
| X | Dev/Prod Parity | Entornos lo más idénticos posible |
| XI | Logs | Flujos de eventos. Agregar externamente |
| XII | Admin Processes | Tareas admin en entornos idénticos a producción |

**Cuándo NO aplica**: Monolitos legacy con estado en filesystem del servidor.

---

## AWS Well-Architected SaaS Lens

**Origen**: AWS SaaS Factory, 2017+
**URL**: https://docs.aws.amazon.com/wellarchitected/latest/saas-lens/

Seis pilares evaluados con cuestionarios técnicos estandarizados:

1. **Excelencia Operativa**: Telemetría de impacto de actividad por tenant
2. **Seguridad**: Aislamiento de cómputo, storage y red. Imposibilidad de
   acceso cruzado entre tenants
3. **Fiabilidad**: Límites por tenant, mitigación de noisy neighbor,
   recuperación ante fallos
4. **Eficiencia de Rendimiento**: Ajuste dinámico de recursos según picos
   de carga de tenants específicos
5. **Optimización de Costos**: Atribución exacta de gasto por tenant
6. **Sostenibilidad**: Minimización de recursos ociosos con pooling

**División fundamental**: Plano de Control vs Plano de Aplicación.

**Cuándo usar**: Diseñar, auditar o migrar plataformas B2B enterprise.
**Cuándo NO**: MVPs experimentales donde velocidad > control de infra.

---

## AWS SaaS Journey Framework

**Origen**: AWS SaaS Factory, 2018.

Estructura la transición de software tradicional (on-premise, licencia) a SaaS:

**Fase 1 — Business Planning**: Alinear precios y empaquetado
**Fase 2 — Product Strategy**: Alcance técnico y priorización de backlog
**Fase 3 — Minimum Viable Service (MVS)**: Plano de control mínimo
  (onboarding básico + identidad + billing)
**Fase 4 — Launch / Go-To-Market**: Lanzamiento, monitoreo, escalado

Principio clave: SaaS es un MODELO DE NEGOCIO, no solo una decisión técnica.
Prohibición estricta de personalización por tenant individual.

---

## Combinación A: Enterprise Next.js Stack

**Patrón de máxima adopción** en startups (2024-2026) por velocidad en TS/JS.

```
┌── Frontend ──────────────────────────────────────┐
│  Next.js (App Router + Server Components)        │
│  → UI interactiva + API Routes serverless        │
├── Autenticación ─────────────────────────────────┤
│  Better Auth (sesiones + tokens)                 │
│  + SAML Jackson (SSO empresarial: Okta/Azure AD) │
├── ORM / Base de Datos ───────────────────────────┤
│  Prisma ORM (queries tipadas + migraciones)      │
│  → PostgreSQL (con RLS para aislamiento)         │
├── Billing ───────────────────────────────────────┤
│  Stripe (Payment Methods API + Webhooks)         │
├── Webhooks / Eventos ────────────────────────────┤
│  Svix (orquestación de webhooks asíncronos)      │
├── Audit Logs ────────────────────────────────────┤
│  Retraced (registros de auditoría para clients)  │
└──────────────────────────────────────────────────┘
```

**Casos de uso**: Plataformas B2B de productividad, HR autoservicio,
portales financieros con integraciones externas.

**Starter kit de referencia**: BoxyHQ `saas-starter-kit` (open source,
docker-compose + Prisma migrations).

**Notas de implementación**:
- Prisma aplica filtros de tenant_id en cada query vía middleware
- Better Auth maneja sesiones globales; SAML Jackson delega a IdPs
- Svix gestiona reintentos y firmas HMAC de webhooks salientes
- Next.js Server Actions para mutations tipadas end-to-end

---

## Combinación B: Laravel Spark-Jetstream Stack

**Ecosistema preferido para bootstrappers** y equipos pequeños (PHP).

```
┌── Framework Base ────────────────────────────────┐
│  Laravel (Monolito integrado: routing, colas,    │
│  validación, Eloquent ORM)                       │
├── UI + Equipos ──────────────────────────────────┤
│  Jetstream (Teams Feature: invitaciones, roles)  │
├── Billing ───────────────────────────────────────┤
│  Laravel Spark (suscripciones Stripe/Paddle,     │
│  pantallas de cobro abstraídas)                  │
├── Multi-tenancy ─────────────────────────────────┤
│  Spatie Multitenancy (conmutación dinámica de    │
│  DB según tenant del request HTTP)               │
└──────────────────────────────────────────────────┘
```

**Casos de uso**: Micro-SaaS para comercios, plataformas educativas,
utilidades de automatización de bajo costo.

**Patrón clave**: El modelo Team de Jetstream implementa la interfaz
IsTenant de Spatie → cada query SQL se ejecuta con scope dinámico
`$model->where('tenant_id', $currentTenant->id)`.

---

## Combinación C: Django SaaS Stack

**Stack para Python-first** con Django 6.0+ (referencia: django-saas-boilerplate).

```
┌── Framework Base ────────────────────────────────┐
│  Django 6.0 (Custom User Model email-only)       │
│  + Background Tasks nativos (@task decorator)    │
├── Autenticación ─────────────────────────────────┤
│  django-allauth (signup, login, email verify)    │
├── Multi-tenancy ─────────────────────────────────┤
│  django-tenants (Schema-per-Tenant automático)   │
│  o manual con tenant_id + Django middleware      │
├── Billing ───────────────────────────────────────┤
│  Stripe (Payment Methods API + Webhooks)         │
├── Frontend ──────────────────────────────────────┤
│  HTMX + Tailwind CSS (CDN) + Alpine.js          │
├── Deployment ────────────────────────────────────┤
│  Gunicorn + WhiteNoise + Procfile                │
│  (Railway / Heroku / VPS ready)                  │
├── Seguridad ─────────────────────────────────────┤
│  Django 6.0 CSP Middleware con nonces            │
│  + HSTS, SSL redirect, secure cookies            │
└──────────────────────────────────────────────────┘
```

**Features de Django 6.0 relevantes para SaaS**:
- `@task` decorator para emails async sin Celery
- CSP middleware nativo con soporte de nonces
- `{% partialdef %}` para componentes UI reutilizables

---

## Herramientas de soporte

### Autenticación y SSO
- **Better Auth / NextAuth.js**: Abstracción de auth web (JS/TS)
- **SAML Jackson**: SSO empresarial open source (Okta, Azure AD)
- **django-allauth**: Auth completo para Django

### Billing
- **Stripe**: Estándar de facto para suscripciones SaaS
- **Paddle**: Alternativa con gestión fiscal integrada (merchant of record)
- **Laravel Spark**: Abstracción de billing para Laravel

### Webhooks y eventos
- **Svix**: Orquestación de webhooks con reintentos y firmas HMAC
- **Inngest**: Event-driven functions (alternativa serverless)

### Audit logs
- **Retraced**: Registros de auditoría para clientes enterprise
- **Audit log propio**: Tabla de eventos con tenant_id, actor, acción, timestamp

### Multi-tenancy
- **Spatie Laravel Multitenancy**: Conmutación dinámica de DB (PHP)
- **django-tenants**: Schema-per-tenant automático (Python)
- **Prisma + RLS**: Pool compartido con políticas a nivel de DB (TS)

---

## Mapa de relaciones

```
AWS SaaS Journey (Negocio) ──prerequisito──→ Twelve-Factor (Código)
                                                     │
                                    ┌────────────────┼───────────────┐
                                    ▼                                ▼
                          AWS WAF SaaS Lens              Patrones Arquitectura
                          (Infra + Seguridad)            (Clean, SOLID, DDD)
                                    │                                │
                                    └──────────┬─────────────────────┘
                                               ▼
                                    Implementación concreta
                                    (Next.js | Laravel | Django)
                                               │
                                    ┌──────────┼──────────┐
                                    ▼          ▼          ▼
                                  Pool      Schema      Silo
                                    │          │          │
                                    └──────────┼──────────┘
                                               ▼
                                    Herramientas de soporte
                                    (Auth, Billing, Webhooks)
```
