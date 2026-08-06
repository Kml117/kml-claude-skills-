# Ruta de Aprendizaje SaaS — Fases 0 a 4

Diseñada para un estudiante que quiere construir un MVP SaaS propio
mientras aprende la teoría de forma progresiva.

---

## Fase 0 — Pre-requisitos

**Duración**: Variable (1-4 semanas según nivel actual)

**Conocimientos necesarios**:
- Bases de datos relacionales: tablas, PKs, FKs, JOINs, índices
- HTTP: métodos (GET/POST/PUT/DELETE), status codes, cookies, headers
- Un lenguaje backend: TypeScript, Python o PHP
- Git básico: commits, branches, push/pull
- Terminal/CLI: navegación, comandos básicos

**Recursos**:
- Documentación oficial de PostgreSQL (gratuita)
- Curso de Introducción al Desarrollo Backend (Platzi, español)

**Checkpoint**: Puedes crear una API REST que haga CRUD contra PostgreSQL
con autenticación básica.

---

## Fase 1 — Fundamentos de Arquitectura Limpia (Semanas 1-4)

**Objetivo**: Entender por qué el código SaaS se estructura diferente al
software tradicional.

**Semana 1**: SaaS como modelo de negocio
- Diferencia entre software local vs suscripción en la nube
- Recurso: Microsoft Learn "SaaS Foundations" (gratis, español, ~45 min)
- Entregable: Resumen escrito de 3 ventajas de SaaS sobre on-premise

**Semana 2**: Twelve-Factor App
- Leer los 12 factores en https://12factor.net (disponible en español)
- Foco especial en: Config (#III), Stateless (#VI), Logs (#XI)
- Entregable: Tabla con cada factor y cómo aplica a tu proyecto

**Semana 3**: Clean Architecture
- Separar dominio de negocio de persistencia y frameworks
- Recurso: Capítulos 1-8 de "Fundamentals of Software Architecture"
- Entregable: Diagrama de capas de tu aplicación

**Semana 4**: Monolito modular
- Configurar un monorepositorio Git
- Estructurar como monolito modular con capas claras
- Variables de entorno estrictas (.env, nunca hardcoded)
- Endpoint de health check sin estado local
- Entregable: Repo funcional con health check y diagrama C4 Context

**Framework dominado**: Twelve-Factor App Methodology

---

## Fase 2 — Práctica de Integración (Semanas 5-10)

**Objetivo**: Construir el aislamiento de datos y el flujo de cobro.

**Semanas 5-6**: Base de datos multiinquilino
- Agregar `tenant_id` indexado en TODAS las tablas transaccionales
- Habilitar Row-Level Security en PostgreSQL
- Crear políticas RLS para cada tabla
- Escribir tests que intenten acceder a datos de otro tenant → 403

**Semanas 7-8**: Middleware de tenant context
- Interceptar cada request HTTP
- Extraer tenant desde subdominio o JWT
- Inyectar tenant_id en contexto de DB (set_config para RLS)
- Si el tenant no es resolvible → fail con 403 (never pass-through)

**Semanas 9-10**: Integración de billing
- Conectar Stripe (o Paddle) para suscripciones
- Crear flujo: registro → selección de plan → checkout → activación
- Webhooks de Stripe: invoice.paid, subscription.updated, etc.
- Vincular estado de suscripción al tenant (active/past_due/canceled)

**Recurso clave**: Capítulos 3 y 8 de "Building Multi-Tenant SaaS
Architectures" (Tod Golding, O'Reilly 2024)

**Entregable**: Backend funcional donde:
1. Se registra un tenant nuevo
2. Se enruta al subdominio dinámico
3. Se asocia login al tenant_id
4. Se impide consultar datos de otros clientes
5. El cobro recurrente funciona sin intervención manual

---

## Fase 3 — Escalado y Control (Semanas 11-20)

**Objetivo**: Separar planos, agregar SSO y telemetría.

**Semanas 11-13**: Separación de planos
- Plano de Control: onboarding, activación, gestión de tenants
- Plano de Aplicación: lógica de negocio consumida por usuarios
- Base de datos del plano de control separada de la de negocio
  (o al menos esquemas separados)

**Semanas 14-16**: Servicios de soporte
- Webhooks outbound con Svix (reintentos, firmas HMAC)
- SSO empresarial básico con SAML Jackson o equivalente
- Portal de administración por tenant (settings, team members)

**Semanas 17-20**: Telemetría y observabilidad
- Logs estructurados con prefijo de tenant (JSON structured logging)
- Métricas de consumo por tenant (requests, storage, compute)
- Alertas por anomalías (noisy neighbor detection)
- Rate limiting por tenant

**Recursos**:
- AWS SaaS Lens (documentación oficial)
- Curso Profesional de Arquitectura de Software (Platzi, avanzado)

**Entregable**: App desplegada en preproducción (Vercel, Railway, AWS)
con aislamiento verificado, webhooks funcionales y CI/CD automatizado.

---

## Fase 4 — Operaciones Enterprise (Mes 6+)

**Objetivo**: FinOps, compliance y preparación para agentes AI.

**FinOps (Atribución de costos)**:
- Etiquetar recursos cloud con tenant_id
- Dashboard de costos desglosados por cliente
- Alertas de consumo anómalo por tenant
- Modelo: costo_marginal_por_tenant = infra / active_tenants

**Bases de datos híbridas**:
- Migrar tenants premium a Database-per-Tenant
- Orquestación con Docker/Kubernetes para DBs dedicadas
- Migraciones en caliente (zero-downtime tenant migration)
- Conexión dinámica según tier del plan

**Compliance y gobernanza**:
- SOC 2 Type II audit trail
- GDPR: derecho a borrado, exportación de datos por tenant
- Ley 1581 (Colombia): registro ante SIC, autorización de titular
- Pen testing por terceros

**Preparación para Agentic AI**:
- Diseñar APIs semánticas que los agentes AI puedan consumir
- Aislamiento de contexto: un agente de un tenant NUNCA accede
  a datos vectoriales o memorias de otro tenant
- Rate limiting y cost caps para operaciones de LLM por tenant

**Recursos**:
- "Building Multi-Tenant SaaS Architectures" (Capítulos 12, 14, 16)
- Paper "Multi-Tenant SaaS Architectures: Design Principles and
  Security Considerations" (Quest Journals, 2024)

**Entregable**: Dashboard operativo real con costos por cliente,
soporte para DBs híbridas y audit trail completo.
