# Seguridad y Aislamiento Multi-Tenant

## Tabla de contenidos
1. [Principios fundamentales](#principios-fundamentales)
2. [Row-Level Security (RLS)](#row-level-security-rls)
3. [Middleware de tenant context](#middleware-de-tenant-context)
4. [Autenticación y SaaS Identity](#autenticación-y-saas-identity)
5. [Anti-patrones de seguridad](#anti-patrones-de-seguridad)
6. [Checklist de auditoría](#checklist-de-auditoría)
7. [Cumplimiento regulatorio](#cumplimiento-regulatorio)

---

## Principios fundamentales

1. **Defensa en profundidad**: El aislamiento NO debe depender de una sola capa.
   Mínimo dos barreras: middleware de aplicación + RLS a nivel de DB.

2. **Fail-closed**: Si no se puede determinar el tenant, la request DEBE fallar
   con 403, nunca pasar sin filtro.

3. **Zero Trust entre tenants**: Tratar cada tenant como si fuera un atacante
   potencial contra los datos de los demás.

4. **Principio de mínimo privilegio**: Los usuarios solo acceden a recursos de
   su propio tenant, nunca a recursos compartidos sin scope explícito.

---

## Row-Level Security (RLS)

RLS es un mecanismo del motor de base de datos (Postgres, SQL Server) que
restringe automáticamente las filas visibles según la sesión de conexión.
Es la última línea de defensa contra fugas de datos.

### Implementación en PostgreSQL

```sql
-- 1. Habilitar RLS en cada tabla de negocio
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;
ALTER TABLE invoices ENABLE ROW LEVEL SECURITY;
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- 2. Crear política de aislamiento
-- USING = filtro en SELECT/UPDATE/DELETE
-- WITH CHECK = validación en INSERT/UPDATE
CREATE POLICY tenant_isolation_orders ON orders
    USING (tenant_id = current_setting('app.current_tenant')::UUID)
    WITH CHECK (tenant_id = current_setting('app.current_tenant')::UUID);

-- 3. El superusuario/admin BYPASSA RLS por defecto.
-- Para jobs de administración, usar un rol dedicado:
CREATE ROLE saas_app_role;
GRANT SELECT, INSERT, UPDATE, DELETE ON orders TO saas_app_role;

-- 4. Forzar RLS incluso para el dueño de la tabla (opcional pero recomendado)
ALTER TABLE orders FORCE ROW LEVEL SECURITY;

-- 5. Función helper para inyectar tenant en la sesión
CREATE OR REPLACE FUNCTION set_tenant_context(p_tenant_id UUID)
RETURNS VOID AS $$
BEGIN
    PERFORM set_config('app.current_tenant', p_tenant_id::TEXT, TRUE);
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
```

### RLS con Prisma (Next.js)

```typescript
// Prisma no soporta RLS nativamente. Usar $executeRaw antes de queries:
async function withTenantContext<T>(
  prisma: PrismaClient,
  tenantId: string,
  fn: (tx: PrismaClient) => Promise<T>
): Promise<T> {
  return prisma.$transaction(async (tx) => {
    await tx.$executeRaw`SELECT set_config('app.current_tenant', ${tenantId}, TRUE)`;
    return fn(tx as PrismaClient);
  });
}

// Uso:
const orders = await withTenantContext(prisma, tenantId, (tx) =>
  tx.order.findMany({ where: { status: 'active' } })
  // RLS filtra automáticamente por tenant_id
);
```

---

## Middleware de tenant context

El middleware es la primera línea de defensa. Intercepta CADA request HTTP
y resuelve el tenant antes de que llegue a la lógica de negocio.

### Estrategias de resolución de tenant

| Estrategia | Ejemplo | Pros | Contras |
|---|---|---|---|
| Subdominio | `acme.app.com` | Claro, SEO-friendly | DNS wildcard necesario |
| Path prefix | `app.com/acme/...` | Sin DNS extra | Colisiones de rutas |
| Header custom | `X-Tenant-ID: uuid` | Flexible para APIs | No funciona en browser |
| JWT claim | `{ tenant_id: "..." }` | Seguro, firmado | Requiere auth previo |
| Domain custom | `acme.com` → CNAME | Marca blanca | Gestión compleja de SSL |

### Implementación en Next.js (subdominio)

```typescript
// middleware.ts
import { NextRequest, NextResponse } from 'next/server';

export async function middleware(request: NextRequest) {
  const hostname = request.headers.get('host') || '';
  const subdomain = hostname.split('.')[0];

  // Ignorar subdominios reservados
  if (['www', 'app', 'api', 'admin'].includes(subdomain)) {
    return NextResponse.next();
  }

  // Resolver tenant desde DB o cache
  const tenant = await resolveTenant(subdomain);

  if (!tenant) {
    return new NextResponse('Tenant not found', { status: 404 });
  }

  if (tenant.status !== 'active') {
    return new NextResponse('Account suspended', { status: 403 });
  }

  // Inyectar tenant context en headers internos
  const headers = new Headers(request.headers);
  headers.set('x-tenant-id', tenant.id);
  headers.set('x-tenant-plan', tenant.plan);

  return NextResponse.next({ request: { headers } });
}
```

### Implementación en Django

```python
# middleware.py
class TenantMiddleware:
    def __init__(self, get_response):
        self.get_response = get_response

    def __call__(self, request):
        hostname = request.get_host().split(':')[0]
        subdomain = hostname.split('.')[0]

        try:
            tenant = Tenant.objects.get(subdomain=subdomain, is_active=True)
        except Tenant.DoesNotExist:
            return HttpResponseNotFound('Tenant not found')

        # Inyectar en el request para que las views lo consuman
        request.tenant = tenant

        # Setear contexto en la sesión de DB para RLS
        with connection.cursor() as cursor:
            cursor.execute(
                "SELECT set_config('app.current_tenant', %s, TRUE)",
                [str(tenant.id)]
            )

        return self.get_response(request)
```

---

## Autenticación y SaaS Identity

SaaS Identity = Identidad global del usuario + Contexto del tenant.

**Flujo correcto**:
1. Usuario se autentica (email/password, OAuth, SAML SSO)
2. Sistema resuelve a qué tenant(s) pertenece
3. Si tiene acceso a múltiples tenants → selector de organización
4. JWT/sesión incluye: `{ user_id, tenant_id, role, plan }`
5. Cada request posterior porta el tenant_id firmado

**Componentes**:

```
┌─ Login ────────────────────────────────────┐
│  Better Auth / django-allauth / NextAuth   │
│  → Autenticación del USUARIO              │
└──────────────────┬─────────────────────────┘
                   ▼
┌─ Tenant Resolver ──────────────────────────┐
│  ¿A qué org pertenece este usuario?        │
│  → Tabla user_memberships (user, tenant,   │
│    role: owner|admin|member|viewer)         │
└──────────────────┬─────────────────────────┘
                   ▼
┌─ Session/Token ────────────────────────────┐
│  JWT firmado con: user_id + tenant_id +    │
│  role + plan + exp                         │
│  O sesión con campos equivalentes          │
└────────────────────────────────────────────┘
```

### SSO Empresarial (SAML/OIDC)

Para clientes enterprise, delegar autenticación a su proveedor de identidad:

- **SAML Jackson** (open source): Integración rápida con Okta, Azure AD,
  Google Workspace
- **Flujo**: Tenant configura su IdP → usuario llega a login →
  redirige al IdP del tenant → vuelve con assertion → crear/actualizar
  usuario → asignar al tenant

### SCIM (Aprovisionamiento automático de usuarios)

Protocolo para que el IdP del cliente gestione usuarios remotamente:
- Crear usuario → se registra automáticamente en el tenant
- Desactivar usuario en el IdP → se desactiva en el SaaS
- Cambiar grupo → se actualiza el rol en el tenant

---

## Anti-patrones de seguridad

### 1. Aislamiento solo por lógica de aplicación

**MAL** (vulnerable):
```typescript
// Un WHERE olvidado = fuga masiva de datos
app.get('/api/orders', async (req, res) => {
  const orders = await prisma.order.findMany();
  // ⚠️ SIN FILTRO DE TENANT → devuelve datos de TODOS
  res.json(orders);
});
```

**BIEN** (con RLS como respaldo):
```typescript
app.get('/api/orders', async (req, res) => {
  // Incluso si olvidas el WHERE, RLS filtra automáticamente
  const orders = await withTenantContext(prisma, req.tenantId, (tx) =>
    tx.order.findMany()
  );
  res.json(orders);
});
```

### 2. Compartir secretos entre tenants

**MAL**: Una sola API key de Stripe para todos los tenants.
**BIEN**: Stripe Connect con cuentas separadas por tenant, o al menos
claves por tenant almacenadas en un vault.

### 3. Logs sin contexto de tenant

**MAL**: `logger.error('Order creation failed')` — ¿de quién?
**BIEN**: `logger.error('Order creation failed', { tenant_id, user_id, order_id })`

### 4. Caché sin namespace de tenant

**MAL**: `redis.set('user:123', data)` — ¿de qué tenant?
**BIEN**: `redis.set('tenant:acme:user:123', data)`

---

## Checklist de auditoría

### Nivel 1 — MVP (Obligatorio)
- [ ] Middleware de tenant context en CADA request
- [ ] tenant_id en TODAS las tablas de negocio
- [ ] RLS habilitado en PostgreSQL
- [ ] Tests de intrusión: request con tenant_id ajeno → 403
- [ ] Variables sensibles en .env, nunca en código
- [ ] HTTPS forzado en producción
- [ ] CORS restringido por dominio de tenant

### Nivel 2 — Growth (Recomendado)
- [ ] Rate limiting por tenant
- [ ] Logs estructurados con tenant_id en cada entrada
- [ ] Webhook signatures con HMAC por tenant
- [ ] Audit trail: quién hizo qué, cuándo
- [ ] Rotación de secretos automatizada
- [ ] Backup y restauración por tenant

### Nivel 3 — Enterprise (Para clientes premium)
- [ ] SSO con SAML/OIDC
- [ ] SCIM para aprovisionamiento automático
- [ ] Database-per-tenant para clientes regulados
- [ ] Encryption at rest con CMK (customer-managed keys)
- [ ] SOC 2 Type II compliance
- [ ] Pen testing por terceros

---

## Cumplimiento regulatorio

| Regulación | Requisito clave para SaaS | Modelo mínimo |
|---|---|---|
| **GDPR** | Derecho a borrado, portabilidad, DPA | Pool + RLS |
| **HIPAA** | Datos médicos aislados físicamente | Silo obligatorio |
| **PCI-DSS** | Datos de tarjeta en scope aislado | Silo o Schema |
| **SOC 2** | Controles de acceso auditables | Pool + audit logs |
| **SOX** | Integridad financiera, segregación | Schema o Silo |
| **Ley 1581 (COL)** | Protección de datos personales, registro | Pool + DPA |

Para Colombia (Ley 1581 de 2012):
- Registro de bases de datos ante la SIC
- Autorización expresa del titular para tratamiento
- Contrato de Encargado de Datos si se procesan datos para terceros
- Aviso de privacidad con finalidades específicas
- Mecanismo de consulta y reclamo
