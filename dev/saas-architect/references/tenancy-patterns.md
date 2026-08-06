# Modelos de Tenencia — Patrones de Aislamiento

## Tabla de contenidos
1. [Shared Database (Pool)](#shared-database-pool)
2. [Schema-per-Tenant](#schema-per-tenant)
3. [Database-per-Tenant (Silo)](#database-per-tenant-silo)
4. [Modelo Híbrido (Bridge)](#modelo-híbrido-bridge)
5. [Matriz de decisión comparativa](#matriz-de-decisión-comparativa)
6. [Implementaciones por stack](#implementaciones-por-stack)

---

## Shared Database (Pool)

**Descripción**: Todos los inquilinos comparten la misma base de datos física y las
mismas tablas. El aislamiento se logra con un campo discriminador `tenant_id` indexado
en cada tabla transaccional.

**Cuándo usar**:
- MVP y primeros 200 inquilinos
- Datos no regulados (no HIPAA, no PCI, no SOX)
- Equipo pequeño (1-3 devs) que prioriza velocidad
- Presupuesto cloud < $500/mes

**Cuándo NO usar**:
- Datos médicos, financieros o legalmente regulados
- Clientes enterprise que exijan certificación SOC 2 Type II
- Más de 1000 inquilinos con patrones de carga muy diferentes

**Implementación obligatoria**:
```sql
-- 1. Campo tenant_id en TODAS las tablas de negocio
CREATE TABLE orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    tenant_id UUID NOT NULL REFERENCES tenants(id),
    -- ... campos de negocio
    created_at TIMESTAMPTZ DEFAULT now()
);

-- 2. Índice compuesto SIEMPRE incluye tenant_id primero
CREATE INDEX idx_orders_tenant ON orders(tenant_id, created_at DESC);

-- 3. Row-Level Security (RLS) como última línea de defensa
ALTER TABLE orders ENABLE ROW LEVEL SECURITY;

CREATE POLICY tenant_isolation ON orders
    USING (tenant_id = current_setting('app.current_tenant')::UUID);

-- 4. Función para inyectar el contexto del tenant en la sesión de DB
CREATE OR REPLACE FUNCTION set_tenant_context(p_tenant_id UUID)
RETURNS VOID AS $$
BEGIN
    PERFORM set_config('app.current_tenant', p_tenant_id::TEXT, TRUE);
END;
$$ LANGUAGE plpgsql;
```

**Riesgo principal**: Si un desarrollador olvida el WHERE tenant_id = ? en un
nuevo endpoint, se exponen datos de TODOS los inquilinos. RLS mitiga esto.

**Costo operativo**: Bajo. Una sola instancia de DB, un solo esquema de
migraciones, un solo backup.

---

## Schema-per-Tenant

**Descripción**: Todos los inquilinos comparten el mismo motor de base de datos
físico, pero cada uno tiene su propio esquema (namespace) SQL. Las tablas dentro
de cada esquema son idénticas pero aisladas lógicamente.

**Cuándo usar**:
- 50-500 inquilinos con requerimientos moderados de aislamiento
- Clientes que exigen separación lógica pero aceptan infraestructura compartida
- Datos sensibles pero no regulados a nivel de DB dedicada

**Cuándo NO usar**:
- Más de 1000 inquilinos (la gestión de esquemas se vuelve compleja)
- Necesidad de escalar storage independientemente por tenant

**Implementación (Postgres)**:
```sql
-- Crear esquema por tenant
CREATE SCHEMA tenant_acme;

-- Las tablas se crean dentro del esquema
CREATE TABLE tenant_acme.orders (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    -- NO necesita tenant_id porque el esquema ya aísla
    created_at TIMESTAMPTZ DEFAULT now()
);

-- Middleware cambia el search_path por request
SET search_path TO tenant_acme, public;
```

**Costo operativo**: Medio. Las migraciones deben ejecutarse en TODOS los
esquemas. Los backups son más complejos (por esquema o de toda la instancia).

---

## Database-per-Tenant (Silo)

**Descripción**: Cada inquilino tiene su propia instancia de base de datos física,
completamente aislada del resto. Ofrece el máximo nivel de seguridad y la
eliminación total del riesgo de fuga de datos.

**Cuándo usar**:
- Datos regulados (HIPAA, PCI-DSS, SOX)
- Clientes enterprise que exigen aislamiento contractual
- Inquilinos con volúmenes de datos o cargas muy desiguales
- Planes "Premium" o "Enterprise" que justifiquen el costo

**Cuándo NO usar**:
- MVP o validación temprana (overhead operativo destruye la agilidad)
- Más de 200 inquilinos sin herramientas de orquestación automatizada

**Implementación**:
- El Plano de Control mantiene un registro de conexiones por tenant
- El middleware resuelve el tenant → busca la cadena de conexión → establece
  la conexión a la DB correcta
- Migraciones se ejecutan como job orquestado contra TODAS las instancias
- Backups, restauraciones y escalado son independientes por tenant

**Costo operativo**: Alto. Cada instancia de DB tiene su propio costo fijo.
Requiere automatización robusta (Terraform, Pulumi, Kubernetes operators).

---

## Modelo Híbrido (Bridge)

**Descripción**: Combina Pool y Silo según el tier del plan. Los inquilinos
Free/Starter comparten una DB Pool con RLS. Los inquilinos Enterprise tienen
DB dedicada (Silo).

**Cuándo usar**:
- Producto maduro con segmentación clara de planes
- Necesidad de ofrecer aislamiento como upsell
- Equilibrio entre eficiencia de costos y cumplimiento regulatorio

**Implementación**:
- La tabla de metadata en el Plano de Control almacena el `isolation_model`
  por tenant: "pool" | "schema" | "silo"
- El connection resolver del middleware lee el modelo y enruta al pool
  compartido o a la DB dedicada
- Feature flags por tenant controlan qué servicios del plano de aplicación
  están disponibles

---

## Matriz de decisión comparativa

| Criterio | Pool (Shared DB) | Schema-per-Tenant | Silo (DB-per-Tenant) | Híbrido |
|---|---|---|---|---|
| **Costo por tenant** | Muy bajo | Bajo | Alto | Variable |
| **Aislamiento** | Lógico (RLS) | Lógico (schema) | Físico | Por tier |
| **Complejidad ops** | Baja | Media | Alta | Alta |
| **Cumplimiento regulatorio** | Básico | Medio | Máximo | Por tier |
| **Riesgo noisy neighbor** | Alto | Medio | Nulo | Por tier |
| **Velocidad de onboarding** | Instantáneo | Segundos | Minutos | Variable |
| **Complejidad migraciones** | Una sola | N esquemas | N databases | Mixta |
| **Ideal para** | MVP, Micro-SaaS | Mid-market | Enterprise | Producto maduro |

---

## Implementaciones por stack

### Next.js + Prisma (Pool con RLS)
```typescript
// middleware.ts — Extraer tenant del subdominio
export function middleware(request: NextRequest) {
  const hostname = request.headers.get('host') || '';
  const subdomain = hostname.split('.')[0];
  // Resolver tenant_id desde subdomain
  // Inyectar en headers para que el API route lo consuma
  const headers = new Headers(request.headers);
  headers.set('x-tenant-id', subdomain);
  return NextResponse.next({ headers });
}

// lib/db.ts — Prisma con extensión de tenant
import { PrismaClient } from '@prisma/client';

export function getTenantPrisma(tenantId: string) {
  const prisma = new PrismaClient();
  // Ejecutar SET app.current_tenant antes de cada query
  return prisma.$extends({
    query: {
      async $allOperations({ operation, args, query }) {
        await prisma.$executeRawUnsafe(
          `SELECT set_config('app.current_tenant', '${tenantId}', TRUE)`
        );
        return query(args);
      },
    },
  });
}
```

### Django (django-tenants para Schema-per-Tenant)
```python
# settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django_tenants.postgresql_backend',
        'NAME': 'saas_db',
    }
}

TENANT_MODEL = 'tenants.Tenant'
TENANT_DOMAIN_MODEL = 'tenants.Domain'

MIDDLEWARE = [
    'django_tenants.middleware.main.TenantMainMiddleware',
    # ... el middleware intercepta el hostname y cambia el schema
]

# models.py
from django_tenants.models import TenantMixin, DomainMixin

class Tenant(TenantMixin):
    name = models.CharField(max_length=100)
    plan = models.CharField(max_length=20, default='starter')
    created_at = models.DateTimeField(auto_now_add=True)

class Domain(DomainMixin):
    pass
```

### Laravel (Spatie Multitenancy para Pool con tenant_id)
```php
// app/Models/Tenant.php
use Spatie\Multitenancy\Models\Tenant as BaseTenant;

class Tenant extends BaseTenant implements UsesTenantConnection
{
    // Spatie intercepta cada request y setea el tenant actual
}

// Cualquier modelo de negocio:
class Order extends Model
{
    protected static function booted()
    {
        // Scope global: TODAS las queries filtran por tenant
        static::addGlobalScope('tenant', function ($query) {
            $query->where('tenant_id', app('currentTenant')->id);
        });

        // Auto-asignar tenant_id al crear
        static::creating(function ($model) {
            $model->tenant_id = app('currentTenant')->id;
        });
    }
}
```
