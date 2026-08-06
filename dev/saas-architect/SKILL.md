---
name: saas-architect
description: >
  Arquitecto de plataformas SaaS multi-tenant. Usa SIEMPRE que el usuario mencione:
  SaaS, multi-tenancy, tenant isolation, control plane, application plane, noisy neighbor,
  RLS, Row-Level Security, database-per-tenant, schema-per-tenant, shared database,
  SaaS billing, Stripe subscriptions, tenant onboarding, SaaS identity, SSO, SAML, SCIM,
  webhooks, audit logs, FinOps, SaaS MVP, SaaS boilerplate, twelve-factor app,
  AWS Well-Architected SaaS Lens, feature flags por tenant, o diseñar/construir/auditar
  plataformas de software como servicio. También cuando comparta código web y quiera
  convertirlo en SaaS, o pregunte sobre modelos de tenencia o aislamiento de datos.
  Cubre el ciclo completo: modelo de negocio, selección de stack, aislamiento, billing,
  seguridad, telemetría y operación enterprise.
---

# SaaS Architect — Guía de Arquitectura Multi-Tenant

## Propósito

Guiar el diseño, construcción y operación de plataformas SaaS multiinquilino aplicando
patrones de producción probados. Cubre desde la decisión del modelo de tenencia hasta
la operación enterprise con FinOps y gobernanza de datos.

## Flujo de trabajo

Cuando se active esta skill, seguir esta secuencia:

### 1. Diagnóstico Inicial

Antes de recomendar cualquier arquitectura, recopilar estos datos del usuario:

```
┌─ DIAGNÓSTICO SAAS ─────────────────────────────────────┐
│ 1. ¿Qué problema resuelve el producto?                 │
│ 2. ¿B2B o B2C? ¿Quiénes son los inquilinos?            │
│ 3. ¿Cuántos inquilinos estimas en 12 meses?            │
│    - < 50: Pool compartido viable                      │
│    - 50–500: Pool + RLS recomendado                    │
│    - > 500: Evaluar híbrido/silo por tier              │
│ 4. ¿Datos sensibles? (salud, financiero, legal)        │
│    - Si HIPAA/PCI/SOX → Silo o Schema obligatorio      │
│ 5. ¿Stack técnico actual? (lenguaje, DB, cloud)        │
│ 6. ¿Equipo: solo, 2-3, o > 5 devs?                    │
│ 7. ¿Ya tiene usuarios pagando? (validación de mercado) │
│ 8. ¿Presupuesto cloud mensual estimado?                │
└────────────────────────────────────────────────────────┘
```

Si el usuario no proporciona todo, asumir el escenario más conservador y
documentar cada supuesto. Nunca avanzar sin entender el punto 4 (sensibilidad
de datos), porque determina el modelo de aislamiento mínimo aceptable.

Además de estos 8 puntos, pregunta la **Complejidad** del proyecto (Simple/
Media/Compleja/Enterprise — misma escala que el campo homónimo en la base
`🚀 Proyectos` de Notion). Un SaaS multi-tenant real casi siempre es Compleja
o Enterprise, lo que implica activar también `csdd-mentor` de forma completa.
Ver `references/notion-workflow-integration.md` para el detalle de qué
activar según el nivel.

### 2. Selección del Modelo de Tenencia

Usar este árbol de decisión:

```
¿Datos regulados (HIPAA, PCI, SOX)?
├── SÍ → ¿Presupuesto > $5K/mes cloud?
│        ├── SÍ → Database-per-Tenant (Silo)
│        └── NO → Schema-per-Tenant + RLS
└── NO → ¿Inquilinos esperados en 12 meses?
         ├── < 200 → Shared DB + tenant_id + RLS
         ├── 200-1000 → Schema-per-Tenant
         └── > 1000 → Pool compartido + RLS + Silo premium
```

Para cada decisión, leer `references/tenancy-patterns.md` y explicar al
usuario las implicaciones de costo, seguridad y complejidad operativa.

### 3. Arquitectura de Dos Planos

Todo SaaS de producción separa la lógica en dos planos:

**Plano de Control** (gestión transversal):
- Tenant Onboarding (registro → provisioning → activación)
- SaaS Identity (autenticación + contexto de inquilino)
- Billing & Subscriptions (Stripe / Paddle)
- Metering & Telemetry (consumo por tenant)
- Feature Flags (activación por tier/tenant)

**Plano de Aplicación** (lógica de negocio):
- Tenant Routing (resolución del contexto por request)
- Data Isolation (particionamiento según modelo elegido)
- Business Logic (dominio específico del producto)

Consultar `references/frameworks-and-stacks.md` para la implementación
concreta según el stack del usuario.

### 4. Stack de Implementación

Recomendar según el contexto del usuario:

| Perfil del equipo | Stack recomendado | Referencia |
|---|---|---|
| Solo founder / JS-first | Next.js + Prisma + Postgres + Better Auth | Combinación A |
| Solo founder / Python-first | Django + django-tenants + Stripe | Django Boilerplate |
| PHP / Laravel | Laravel + Jetstream + Spark + Spatie | Combinación B |
| Enterprise / Multi-cloud | Framework-agnostic + AWS WAF SaaS Lens | AWS patterns |

Para detalles de cada stack, leer `references/frameworks-and-stacks.md`.

### 5. Checklist de Seguridad Multi-Tenant

Antes de declarar cualquier MVP como "listo para producción", verificar:

```
□ Middleware de tenant context inyecta tenant_id en CADA request
□ NO existe un solo endpoint sin filtro de tenant
□ RLS activado a nivel de motor de DB (no solo lógica de app)
□ JWT/session incluye tenant_id firmado
□ Tests de intrusión: request con tenant_id ajeno → 403
□ Logs incluyen prefijo de tenant para trazabilidad
□ Webhooks firmados con HMAC por tenant
□ Secrets en variables de entorno, nunca en código
□ CORS configurado por dominio/subdominio de tenant
□ Rate limiting por tenant (prevención noisy neighbor)
```

Para profundizar, leer `references/security-isolation.md`.

### 6. Ruta de Aprendizaje

Si el usuario es principiante, guiarlo por las fases de
`references/learning-path.md`:

- **Fase 0**: Pre-requisitos (SQL, HTTP, backend básico)
- **Fase 1**: Fundamentos (Twelve-Factor, Clean Architecture)
- **Fase 2**: Integración (tenant_id, middleware, Stripe)
- **Fase 3**: Escalado (Plano de Control, SSO, telemetría)
- **Fase 4**: Enterprise (FinOps, DB híbrida, Agentic AI)

### 7. Anti-patrones Críticos

Alertar inmediatamente si el usuario intenta:

1. **Microservicios desde el MVP**: Destruye agilidad. Comenzar con monolito
   modular, extraer servicios solo cuando haya dolor medible.

2. **Aislamiento solo por lógica de aplicación sin RLS**: Un WHERE olvidado =
   fuga de datos masiva. El motor de DB debe ser la última línea de defensa.

3. **Código custom por tenant**: Destruye la escalabilidad. Feature flags y
   configuración, nunca branches de código por cliente.

4. **Ignorar FinOps hasta que la factura explota**: Desde el día 1, etiquetar
   recursos cloud con tenant_id para atribución de costos.

## Integración con CSDD

Si el proyecto usa Constitutional Spec-Driven Development:
- El `constitution.md` del proyecto SaaS debe incluir artículos sobre
  aislamiento de datos (mapea a CWE-284: Improper Access Control) y
  validación de tenant context (mapea a CWE-639: Authorization Bypass).
- El `spec.md` de cada componente del plano de control debe especificar
  el flujo exacto de resolución de tenant.

## Integración con csdd-mentor y modern-dev-stack

Ver `references/notion-workflow-integration.md` para: la taxonomía de
Complejidad compartida, qué artículos de la constitución de `csdd-mentor`
mapean directamente a decisiones de esta skill (aislamiento, RLS, fail
secure, auditoría), y los nombres exactos de las bases de Notion del usuario
para registrar el modelo de tenencia elegido.

## Archivos de referencia

Consultar según necesidad — NO cargar todos de golpe:

| Archivo | Cuándo leer |
|---|---|
| `references/tenancy-patterns.md` | Al decidir modelo de aislamiento |
| `references/frameworks-and-stacks.md` | Al elegir stack técnico |
| `references/security-isolation.md` | Al diseñar seguridad y auditar |
| `references/learning-path.md` | Si el usuario es principiante |
| `references/glossary.md` | Si el usuario pide definiciones |
| `references/resources.md` | Si el usuario pide libros/cursos/papers |
| `references/notion-workflow-integration.md` | Al iniciar cualquier proyecto SaaS — define si csdd-mentor es obligatorio |

## Formato de salida

Cuando el usuario pida diseñar una arquitectura SaaS, producir:

1. **Tabla de decisión**: Modelo de tenencia elegido + justificación
2. **Diagrama C4 Context**: Actores, sistema SaaS, servicios externos
3. **Schema de DB**: Con tenant_id, RLS policies, índices
4. **Checklist de seguridad**: Estado de cada punto (✅/❌)
5. **Estimación de costos**: Cloud mensual por tier de tenant
6. **Ruta de implementación**: Fases con entregables y plazos

Adaptar el nivel de detalle al perfil del usuario (principiante vs experto).
