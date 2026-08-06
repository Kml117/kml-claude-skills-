# Autenticación y Seguridad — Clerk, Better Auth, Turnstile

## Tabla de contenidos
1. [Clerk vs Better Auth vs Auth.js](#comparativa)
2. [CVE-2025-29927 — Bypass de middleware](#cve-2025-29927)
3. [Cloudflare Turnstile — CAPTCHA invisible](#turnstile)
4. [Rate limiting](#rate-limiting)
5. [Validación de inputs con Zod](#validación-con-zod)
6. [Correo transaccional con Resend](#resend)
7. [Checklist de seguridad](#checklist)

---

## Comparativa

| Criterio | Clerk | Better Auth | Auth.js v5 |
|---|---|---|---|
| **Datos de usuario** | Servidores de Clerk | Tu PostgreSQL | Tu DB (adaptadores) |
| **Configuración** | Baja (widgets UI incluidos) | Moderada (init + client) | Alta (config compleja) |
| **Edge compatible** | ✅ Validación crypto local | ✅ Workers/Bun nativo | ✅ @auth/core |
| **Costo a escala** | Hasta $4K/mes @200K MAU | $0 (tu servidor) | $0 (tu servidor) |
| **Free tier** | 50K MAU | Ilimitado | Ilimitado |
| **SSO/SAML** | Plan Pro ($$$) | Plugin + SAML Jackson | Config manual |
| **2FA** | Incluido | Plugin nativo | Manual |
| **Passkeys** | Incluido | Plugin nativo | Experimental |
| **Vendor lock-in** | Alto (datos en Clerk) | Ninguno | Bajo |
| **CVE-2025-29927** | Mitigado (JWKS crypto) | Requiere cookie validation | Depende del dev |

### Cuándo usar Clerk
- MVP donde la velocidad de lanzamiento es prioridad absoluta
- Menos de 50K MAU (free tier generoso)
- Equipo sin experiencia en auth
- No importa el vendor lock-in en esta etapa

### Cuándo usar Better Auth
- Control total de datos (Postgres propio)
- Proyectos que escalarán más allá de 50K MAU
- Cumplimiento regulatorio (GDPR, Ley 1581)
- Stack TypeScript que valora cero dependencias externas

### Cuándo usar Auth.js v5
- Migración de proyectos con NextAuth v4
- Multi-provider OAuth (Google, GitHub, etc.)
- Cuando no se necesita SSO enterprise

### Costo real de auth custom
Según datos de la industria:
- Proyección inicial: 1-3 meses de desarrollo
- Realidad: 74% de empresas reporta 3-9 meses
- Costo de ingeniería SSO enterprise: $250K-$500K
- Mantenimiento continuo: 15-20% del tiempo de dev

Por esto se recomienda usar soluciones probadas, no reinventar.

---

## CVE-2025-29927

**Vulnerabilidad**: Las validaciones de sesión en middleware de Next.js
(edge) podían ser bypaseadas con el header `x-middleware-subrequest`.

**Impacto**: Un atacante podía acceder a rutas protegidas sin autenticación.

**Mitigación por solución**:

```typescript
// ❌ VULNERABLE — Auth SOLO en middleware
// middleware.ts
export function middleware(req) {
  const session = getSession(req); // Edge validation only
  if (!session) return redirect('/login');
}

// ✅ SEGURO — Validación TAMBIÉN en server-side

// Clerk: Validación crypto contra JWKS
import { auth } from '@clerk/nextjs/server';
export default async function Page() {
  const { userId } = await auth();
  if (!userId) redirect('/login');
}

// Better Auth: Validación de cookie con acceso a DB
import { auth } from '@/lib/auth';
export default async function Page() {
  const session = await auth.api.getSession({
    headers: await headers(),
  });
  if (!session) redirect('/login');
}
```

**Regla**: Nunca confiar ÚNICAMENTE en middleware edge para auth.
Validar TAMBIÉN en cada Server Component o Server Action que acceda
a datos protegidos.

---

## Turnstile

Cloudflare Turnstile reemplaza CAPTCHAs visuales con validación invisible
basada en telemetría pasiva y desafíos criptográficos.

### Frontend (Next.js)
```typescript
'use client';
import { Turnstile } from '@marsidev/react-turnstile';

export function ContactForm() {
  const [token, setToken] = useState('');

  return (
    <form action={submitForm}>
      {/* campos del formulario */}
      <Turnstile
        siteKey={process.env.NEXT_PUBLIC_TURNSTILE_SITE_KEY!}
        onSuccess={setToken}
      />
      <input type="hidden" name="turnstile-token" value={token} />
      <button type="submit">Enviar</button>
    </form>
  );
}
```

### Backend — Validación del token
```typescript
// lib/turnstile.ts
export async function validateTurnstile(token: string): Promise<boolean> {
  const response = await fetch(
    'https://challenges.cloudflare.com/turnstile/v0/siteverify',
    {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({
        secret: process.env.TURNSTILE_SECRET_KEY,
        response: token,
      }),
    }
  );
  const data = await response.json();
  return data.success === true;
}

// Server Action
'use server';
export async function submitForm(formData: FormData) {
  const token = formData.get('turnstile-token') as string;

  // 1. Validar CAPTCHA en server-side (OBLIGATORIO)
  const isHuman = await validateTurnstile(token);
  if (!isHuman) throw new Error('CAPTCHA validation failed');

  // 2. Validar inputs con Zod
  // 3. Rate limit check
  // 4. Procesar el formulario
}
```

**Anti-patrón**: Validar Turnstile solo en frontend. Un bot puede enviar
el formulario directamente al endpoint sin el token.

---

## Rate limiting

Proteger endpoints públicos contra abuso con contadores por IP.

### Con Upstash Redis (serverless)
```typescript
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(5, '60 s'), // 5 requests por minuto
  analytics: true,
});

export async function rateLimitCheck(identifier: string) {
  const { success, limit, remaining, reset } = await ratelimit.limit(identifier);
  
  if (!success) {
    throw new Error(`Rate limit exceeded. Try again in ${reset - Date.now()}ms`);
  }
  
  return { remaining };
}

// En Server Action:
const ip = (await headers()).get('x-forwarded-for') ?? '127.0.0.1';
await rateLimitCheck(ip);
```

**No usar memoria serverless** para rate limiting — la memoria se resetea
entre invocaciones. Persistir siempre en Redis (Upstash o Vercel KV).

### Capas complementarias
1. **Turnstile**: Primera línea contra bots
2. **Rate limiting por IP**: Limitar requests por minuto
3. **Honeypot fields**: Campo invisible que los bots llenan
4. **Cloudflare Bot Fight Mode**: Protección a nivel de CDN

---

## Validación con Zod

Toda entrada del usuario debe validarse en el server-side con esquemas Zod.

```typescript
import { z } from 'zod';

// Definir esquema
const contactSchema = z.object({
  name: z.string().min(2).max(100),
  email: z.string().email(),
  message: z.string().min(10).max(2000),
  company: z.string().optional(),
});

// En Server Action
'use server';
export async function submitContact(formData: FormData) {
  // 1. Parsear y validar
  const result = contactSchema.safeParse({
    name: formData.get('name'),
    email: formData.get('email'),
    message: formData.get('message'),
    company: formData.get('company'),
  });

  if (!result.success) {
    return { error: result.error.flatten().fieldErrors };
  }

  // 2. Datos validados y tipados
  const { name, email, message } = result.data;
  // ... procesar
}
```

---

## Resend

Resend es el proveedor preferido para correo transaccional en TypeScript.

### Configuración DNS obligatoria
1. **DKIM**: Firma criptográfica del dominio emisor
2. **SPF**: Autorizar servidores de Resend a enviar desde tu dominio
3. **Return-path**: Configurar ruta de retorno para bounces

### Implementación
```typescript
import { Resend } from 'resend';

const resend = new Resend(process.env.RESEND_API_KEY);

export async function sendWelcomeEmail(to: string, name: string) {
  const { data, error } = await resend.emails.send({
    from: 'Cifra <hola@usecifra.io>',
    to,
    subject: `Bienvenido a Cifra, ${name}`,
    html: `<p>Hola ${name}, tu cuenta está activa.</p>`,
  });

  if (error) {
    console.error('Email send failed:', { to, error });
    // No lanzar error — el email es secundario al flujo principal
  }

  return data;
}
```

### Flujo completo en Server Action
```
1. Validar inputs (Zod)
2. Validar CAPTCHA (Turnstile)
3. Check rate limit (Upstash Redis)
4. Procesar lógica de negocio
5. Enviar email (Resend) — async, no bloquear respuesta
6. Retornar resultado al cliente
```

---

## Checklist

### Nivel 1 — MVP
- [ ] Auth con Clerk o Better Auth (no custom)
- [ ] Validación server-side con Zod en TODOS los inputs
- [ ] Turnstile en formularios públicos
- [ ] Rate limiting por IP en endpoints sensibles
- [ ] Variables sensibles en .env, nunca en código
- [ ] HTTPS forzado
- [ ] Auth validado en server-side, no solo middleware

### Nivel 2 — Growth
- [ ] 2FA / MFA habilitado
- [ ] Honeypot fields en formularios
- [ ] CSP headers configurados
- [ ] CORS restringido por dominio
- [ ] Session rotation después de login
- [ ] Audit trail de acciones sensibles

### Nivel 3 — Enterprise
- [ ] SSO con SAML/OIDC
- [ ] SCIM para aprovisionamiento automático
- [ ] SOC 2 compliance
- [ ] Pen testing por terceros
- [ ] Rotación automática de secretos
