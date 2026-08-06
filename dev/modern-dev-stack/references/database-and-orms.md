# Bases de Datos y ORMs — Selección e Implementación

## Tabla de contenidos
1. [Panorama de bases de datos 2025](#panorama-2025)
2. [PostgreSQL — El estándar](#postgresql)
3. [Redis — Cache y tiempo real](#redis)
4. [SQLite — Desarrollo local y edge](#sqlite)
5. [Prisma vs Drizzle — Comparativa profunda](#prisma-vs-drizzle)
6. [Anti-patrones críticos de ORM](#anti-patrones)
7. [Patrones de migración](#patrones-de-migración)

---

## Panorama 2025

| Base de datos | Cuota 2025 | Tendencia | Escenario ideal |
|---|---|---|---|
| **PostgreSQL** | 55.6% | Dominante estable | SaaS, transacciones complejas, RLS |
| **MySQL** | 40.5% | Ligera baja | CMS, WordPress, legacy |
| **SQLite** | 37.5% | En crecimiento | Dev local, mobile, edge, analytics |
| **Redis** | 28.0% | Fuerte crecimiento (+8% YoY) | Cache, sesiones, colas, rate limiting |

---

## PostgreSQL

**Por qué es el estándar para proyectos nuevos**:
- Transacciones ACID completas
- Row-Level Security (RLS) nativo para multi-tenancy
- Extensibilidad: JSONB, full-text search, PostGIS
- Concurrencia robusta sin degradación
- Soporte de todos los ORMs modernos

**Servicios gestionados recomendados**:
- **Neon**: Serverless Postgres, branching de DB, free tier generoso
- **Supabase**: Postgres + Auth + Realtime + Storage (BaaS)
- **Railway**: Managed Postgres simple, pricing por uso
- **Vercel Postgres**: Integrado con Vercel, powered by Neon

**Configuración esencial**:
```sql
-- Siempre usar UUIDs como PKs (no auto-increment)
CREATE EXTENSION IF NOT EXISTS "pgcrypto";

CREATE TABLE users (
    id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
    email TEXT UNIQUE NOT NULL,
    name TEXT NOT NULL,
    created_at TIMESTAMPTZ DEFAULT now(),
    updated_at TIMESTAMPTZ DEFAULT now()
);

-- Índice en campos frecuentemente filtrados
CREATE INDEX idx_users_email ON users(email);

-- Trigger para auto-update de updated_at
CREATE OR REPLACE FUNCTION update_timestamp()
RETURNS TRIGGER AS $$
BEGIN
    NEW.updated_at = now();
    RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER set_updated_at
    BEFORE UPDATE ON users
    FOR EACH ROW EXECUTE FUNCTION update_timestamp();
```

---

## Redis

**Casos de uso principales**:
- **Cache**: Respuestas de API, queries de DB costosas
- **Sesiones**: Session store para auth (reemplaza cookies pesadas)
- **Rate limiting**: Contadores por IP/usuario con TTL
- **Colas**: Background jobs con BullMQ o similar
- **Pub/Sub**: Notificaciones en tiempo real

**Servicios recomendados**:
- **Upstash Redis**: Serverless, pay-per-request, free tier
- **Vercel KV**: Powered by Upstash, integrado con Vercel
- **Redis Cloud**: Managed Redis oficial

**Patrón de cache con invalidación**:
```typescript
import { Redis } from '@upstash/redis';

const redis = new Redis({ url: process.env.REDIS_URL, token: process.env.REDIS_TOKEN });

async function getCachedData<T>(key: string, fetcher: () => Promise<T>, ttl = 3600): Promise<T> {
  const cached = await redis.get<T>(key);
  if (cached) return cached;
  
  const fresh = await fetcher();
  await redis.set(key, fresh, { ex: ttl });
  return fresh;
}

// Uso: cache de 1 hora para dashboard stats
const stats = await getCachedData(
  `stats:${tenantId}`,
  () => db.query.orders.aggregate({ ... }),
  3600
);
```

---

## SQLite

**Cuándo usarlo**:
- Desarrollo local (zero config, un archivo)
- Prototipos y MVPs ultra-rápidos
- Apps mobile (React Native, Flutter)
- Edge functions (Cloudflare D1, Turso)
- Analytics locales y CLI tools

**Cuándo NO usarlo**:
- Múltiples escritores concurrentes (single-writer lock)
- Multi-tenancy (sin RLS nativo robusto)
- Aplicaciones con más de ~100 usuarios concurrentes

**Servicios edge**:
- **Turso**: SQLite distribuido (libSQL), replicación global
- **Cloudflare D1**: SQLite en edge workers

---

## Prisma vs Drizzle

### Comparativa técnica

| Métrica | Prisma (v7+) | Drizzle ORM |
|---|---|---|
| **Filosofía** | Schema-first (archivo .prisma) | Code-first (TypeScript puro) |
| **Compilación** | Requiere `prisma generate` | Ninguna, tipos inferidos |
| **Bundle size** | ~1.6 MB (engine Rust) | ~12.2 KB |
| **Cold start serverless** | 500ms–1s (engine init) | <10ms |
| **Estilo de queries** | API abstracta OOP | SQL explícito en métodos TS |
| **Studio / GUI** | Prisma Studio (maduro) | Drizzle Studio (en maduración) |
| **Migraciones** | `prisma migrate` (robusto) | `drizzle-kit` (robusto) |
| **Comunidad** | Más grande, más docs | Creciendo rápido |

### Cuándo usar Prisma
- Equipos que priorizan velocidad de desarrollo sobre rendimiento
- Proyectos que NO son serverless/edge
- Prisma Studio es valioso para depuración visual
- El schema declarativo facilita onboarding de nuevos devs

### Cuándo usar Drizzle
- Serverless / edge (cold start es crítico)
- Proyectos donde el bundle size importa
- Devs que prefieren control directo del SQL generado
- Aplicaciones de alto rendimiento

### Definición de esquema — comparación lado a lado

**Prisma** (schema.prisma):
```prisma
model User {
  id        String   @id @default(uuid())
  email     String   @unique
  name      String
  orders    Order[]
  createdAt DateTime @default(now())
  
  @@index([email])
}

model Order {
  id        String   @id @default(uuid())
  userId    String
  user      User     @relation(fields: [userId], references: [id])
  total     Decimal
  status    String   @default("pending")
  createdAt DateTime @default(now())
  
  @@index([userId])  // ← DECLARAR EXPLÍCITAMENTE
}
```

**Drizzle** (schema.ts):
```typescript
import { pgTable, uuid, text, decimal, timestamp, index } from 'drizzle-orm/pg-core';
import { relations } from 'drizzle-orm';

export const users = pgTable('users', {
  id: uuid('id').primaryKey().defaultRandom(),
  email: text('email').unique().notNull(),
  name: text('name').notNull(),
  createdAt: timestamp('created_at').defaultNow(),
}, (table) => ({
  emailIdx: index('idx_users_email').on(table.email),
}));

export const orders = pgTable('orders', {
  id: uuid('id').primaryKey().defaultRandom(),
  userId: uuid('user_id').notNull().references(() => users.id), // ← FK REAL en DB
  total: decimal('total', { precision: 10, scale: 2 }),
  status: text('status').default('pending'),
  createdAt: timestamp('created_at').defaultNow(),
}, (table) => ({
  userIdx: index('idx_orders_user').on(table.userId), // ← ÍNDICE EXPLÍCITO
}));

// relations() = metadatos para API de queries, NO crea FK en DB
export const usersRelations = relations(users, ({ many }) => ({
  orders: many(orders),
}));

export const ordersRelations = relations(orders, ({ one }) => ({
  user: one(users, { fields: [orders.userId], references: [users.id] }),
}));
```

---

## Anti-patrones

### 1. `db push` en producción
```bash
# ❌ NUNCA en producción — puede borrar datos
npx prisma db push
npx drizzle-kit push

# ✅ SIEMPRE usar migraciones
npx prisma migrate deploy
npx drizzle-kit migrate
```

`db push` sincroniza el schema destructivamente. Las migraciones generan
archivos SQL versionados que se pueden revisar, revertir y auditar.

### 2. Foreign keys sin índice
Prisma y Drizzle NO crean índices automáticos en foreign keys.
Sin índice, los JOINs degradan exponencialmente con el volumen de datos.

```prisma
// ❌ Sin índice en userId — JOIN lento
model Order {
  userId String
  user   User @relation(fields: [userId], references: [id])
}

// ✅ Con índice explícito
model Order {
  userId String
  user   User @relation(fields: [userId], references: [id])
  @@index([userId])
}
```

### 3. Confundir `relations()` con FK reales (Drizzle)
```typescript
// ❌ Esto NO crea FK en la base de datos real
export const ordersRelations = relations(orders, ({ one }) => ({
  user: one(users, { fields: [orders.userId], references: [users.id] }),
}));

// ✅ ESTO sí crea la FK real en la DB
userId: uuid('user_id').notNull().references(() => users.id),
```

`relations()` son metadatos para la API de queries de Drizzle.
`.references()` en la definición de columna crea la restricción real.
Se necesitan AMBOS.

### 4. No usar transacciones para operaciones multi-tabla
```typescript
// ❌ Si falla el segundo insert, queda inconsistente
await db.insert(orders).values(orderData);
await db.insert(orderItems).values(itemsData);

// ✅ Transacción atómica
await db.transaction(async (tx) => {
  const [order] = await tx.insert(orders).values(orderData).returning();
  await tx.insert(orderItems).values(
    itemsData.map(item => ({ ...item, orderId: order.id }))
  );
});
```

---

## Patrones de migración

### Flujo correcto de migraciones

```
1. Modificar schema (Prisma o Drizzle)
2. Generar migración:
   prisma migrate dev --name add_orders_table
   drizzle-kit generate --name add_orders_table
3. Revisar el SQL generado (nunca aplicar a ciegas)
4. Aplicar en dev: prisma migrate dev / drizzle-kit migrate
5. Commit del archivo de migración al repo
6. En CI/CD: prisma migrate deploy / drizzle-kit migrate
```

### Migraciones destructivas
Antes de borrar columnas o tablas:
1. Crear nueva columna/tabla
2. Migrar datos con script
3. Verificar integridad
4. Eliminar la antigua en una migración posterior
