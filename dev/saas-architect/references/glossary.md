# Glosario SaaS — 30 Términos Clave

| # | Término | Definición | Relacionado con |
|---|---|---|---|
| 1 | **SaaS** | Modelo donde la app es alojada centralmente y accedida por múltiples clientes vía internet por suscripción | Tenant, Multi-tenancy |
| 2 | **Tenant (Inquilino)** | Organización o grupo de usuarios con acceso lógico propio dentro del sistema multiinquilino | SaaS, Aislamiento |
| 3 | **Control Plane** | Capa que gestiona onboarding, billing, aprovisionamiento. No maneja lógica de negocio | Application Plane |
| 4 | **Application Plane** | Capa con la lógica de negocio, algoritmos y datos transaccionales del usuario final | Control Plane |
| 5 | **Multi-tenancy** | Una instancia de la app sirve a múltiples clientes compartiendo recursos físicos | Single-tenancy |
| 6 | **Single-tenancy** | Infraestructura dedicada por cliente individual | Multi-tenancy, Silo |
| 7 | **Modelo Silo** | Recursos físicos dedicados por tenant. Máximo aislamiento, máximo costo | Pool, Single-tenancy |
| 8 | **Modelo Pool** | Recursos compartidos, divididos lógicamente entre tenants | Silo, Noisy Neighbor |
| 9 | **Onboarding** | Proceso automatizado de registrar, validar, facturar y activar un nuevo tenant | Control Plane |
| 10 | **Noisy Neighbor** | Tenant que consume recursos excesivos y degrada el servicio de los demás | Pool, Rate Limiting |
| 11 | **Tenant Isolation** | Políticas y mecanismos que previenen acceso cruzado entre tenants | Seguridad, RLS |
| 12 | **SaaS Identity** | Vínculo entre identidad global del usuario y contexto/privilegios de su tenant | Onboarding, JWT |
| 13 | **RLS (Row-Level Security)** | Mecanismo de DB que restringe filas visibles según la sesión de conexión | Tenant Isolation |
| 14 | **Shared Database** | Todos los datos en el mismo motor de DB, aislados por campo discriminador | RLS, Noisy Neighbor |
| 15 | **Database-per-Tenant** | DB exclusiva por tenant. Elimina riesgo de fuga de datos | Silo, Escalabilidad |
| 16 | **Schema-per-Tenant** | Mismo motor de DB, esquemas SQL separados por tenant | Shared DB, DB-per-Tenant |
| 17 | **JWT** | Token compacto firmado que transmite identidad y tenant_id entre cliente y servidor | SaaS Identity |
| 18 | **Subdominio dinámico** | URL personalizada por tenant (empresa.app.com) para resolver contexto | Tenant Routing |
| 19 | **Tenant Context** | Variables del tenant activo (ID, plan, región) que fluyen en cada request | SaaS Identity |
| 20 | **Stateless** | Procesos que no almacenan datos de sesión en memoria local | Twelve-Factor |
| 21 | **Backing Services** | Recursos externos consumidos por red: DB, Redis, colas de mensajes | Twelve-Factor |
| 22 | **Modular Monolith** | Una base de código desplegada junta pero dividida en módulos independientes | Microservicios |
| 23 | **Microservicios** | Servicios autónomos pequeños, desplegables por separado, comunicados por APIs | Monolito Modular |
| 24 | **SSO (Single Sign-On)** | Autenticación centralizada: una credencial para múltiples sistemas | SAML Jackson |
| 25 | **SCIM** | Protocolo para aprovisionar/desaprovisionar usuarios remotamente entre plataformas | SSO |
| 26 | **FinOps** | Gestión financiera de la nube: monitorear y optimizar costo por tenant | Control Plane |
| 27 | **Rate Limiting** | Restricción de requests por tenant en un intervalo temporal | Noisy Neighbor |
| 28 | **Circuit Breaker** | Patrón que interrumpe requests a servicios externos con fallas repetitivas | Fiabilidad |
| 29 | **Feature Flags** | Configuración en caliente para activar/desactivar funciones por tenant/tier | Control Plane |
| 30 | **Twelve-Factor App** | Metodología de 12 principios para apps nativas en la nube sin estado local | Stateless |
