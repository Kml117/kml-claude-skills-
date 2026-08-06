# Arquitectura Frontend — SPA, SSR y Next.js

## Tabla de contenidos
1. [Evolución de las arquitecturas web](#evolución)
2. [Comparativa de modelos de renderizado](#comparativa)
3. [Next.js App Router — Modelo híbrido](#nextjs-app-router)
4. [Server Components vs Client Components](#server-vs-client-components)
5. [Cuándo usar cada modelo](#cuándo-usar-cada-modelo)

---

## Evolución

```
Multi-Page (MPA)           SPA Tradicional           Híbrido (Next.js)
┌──────────────┐         ┌──────────────┐         ┌──────────────┐
│ Server render│         │ Client render│         │ Server + Client│
│ cada página  │         │ todo en JS   │         │ por componente │
│ + recarga    │         │ + SPA routing│         │ + streaming    │
│   completa   │         │ + bundle     │         │ + precarga     │
│              │         │   grande     │         │   selectiva    │
└──────────────┘         └──────────────┘         └──────────────┘
SEO: ✅ Excelente        SEO: ❌ Pobre             SEO: ✅ Nativo
UX:  ❌ Lenta            UX:  ✅ Fluida            UX:  ✅ Fluida
Load: ✅ Rápido           Load: ❌ Bundle pesado     Load: ✅ Streaming
```

**Problema de las SPA puras**: Los rastreadores web reciben HTML vacío,
los bundles JS pesados degradan la carga inicial, y la gestión de estado
se vuelve compleja (Redux, Zustand, Context API).

**Solución 2025-2026**: Arquitecturas híbridas donde el servidor renderiza
el HTML inicial y solo hidrata selectivamente los componentes interactivos.

---

## Comparativa

| Dimensión | MPA Clásico | SPA Tradicional | Next.js App Router |
|---|---|---|---|
| **Navegación** | Recarga completa por ruta | Virtual, instantánea (JS) | Híbrida con precarga async |
| **Rendimiento inicial** | Rápido (HTML listo) | Lento (bundle JS grande) | Muy rápido (Server Components) |
| **SEO** | Excelente nativo | Deficiente sin pre-render | Nativo + metadatos dinámicos |
| **Estado** | Sesión en servidor | Redux/Zustand/Context | Server state + hidratación auto |
| **Complejidad** | Baja | Alta (SPA routing, state) | Media (convenciones claras) |

---

## Next.js App Router

Next.js 14-16 con App Router es el estándar de referencia en 2025-2026:

### Estructura de proyecto
```
app/
├── layout.tsx          ← Layout raíz (Server Component por defecto)
├── page.tsx            ← Página principal
├── globals.css
├── api/
│   └── webhook/
│       └── route.ts    ← API Route (endpoint HTTP)
├── dashboard/
│   ├── layout.tsx      ← Layout anidado
│   ├── page.tsx        ← /dashboard
│   └── [id]/
│       └── page.tsx    ← /dashboard/:id (ruta dinámica)
└── (auth)/
    ├── login/
    │   └── page.tsx    ← /login (grupo de rutas)
    └── register/
        └── page.tsx    ← /register
```

### Conceptos clave

**Server Components** (por defecto en App Router):
- Se ejecutan SOLO en el servidor
- Acceso directo a DB, filesystem, variables de entorno
- NO envían JavaScript al cliente
- NO pueden usar useState, useEffect, onClick

**Client Components** (marcados con `'use client'`):
- Se ejecutan en el navegador
- Necesarios para interactividad: formularios, modales, animaciones
- Pueden usar hooks de React y event handlers

**Server Actions** (funciones `'use server'`):
- Mutations tipo RPC directamente desde el cliente
- Reemplazan la necesidad de API Routes para operaciones CRUD
- Incluyen revalidación automática de caché

### Patrón recomendado
```typescript
// app/dashboard/page.tsx — Server Component (por defecto)
import { db } from '@/lib/db';
import { DashboardClient } from './dashboard-client';

export default async function DashboardPage() {
  // Acceso directo a DB — NO va al cliente
  const data = await db.query.orders.findMany();
  
  // Pasa datos al Client Component para interactividad
  return <DashboardClient initialData={data} />;
}

// app/dashboard/dashboard-client.tsx — Client Component
'use client';
import { useState } from 'react';

export function DashboardClient({ initialData }) {
  const [filter, setFilter] = useState('all');
  // Interactividad aquí: filtros, modales, etc.
  return (/* JSX interactivo */);
}
```

---

## Server vs Client Components

### Regla general
**Server Component** si el componente:
- Solo muestra datos (lectura)
- Accede a DB o filesystem
- Usa variables de entorno del servidor
- No necesita interactividad

**Client Component** si el componente:
- Usa `useState`, `useEffect`, `useRef`
- Responde a eventos (`onClick`, `onChange`)
- Usa APIs del navegador (localStorage, geolocation)
- Necesita animaciones o transiciones

### Patrón de composición
```
Server Component (layout.tsx)
├── Server Component (header — datos estáticos)
├── Client Component (search bar — interactivo) ← 'use client'
├── Server Component (data table — lectura de DB)
│   └── Client Component (row actions — botones) ← 'use client'
└── Server Component (footer — estático)
```

La clave es mantener el `'use client'` lo más abajo posible en el árbol
de componentes para minimizar el JavaScript enviado al navegador.

---

## Cuándo usar cada modelo

| Proyecto | Modelo recomendado | Justificación |
|---|---|---|
| Blog / marketing | Next.js SSG | SEO + rendimiento estático |
| Dashboard interno | Next.js SSR | Datos en tiempo real, sin SEO |
| E-commerce | Next.js ISR | SEO + datos actualizados |
| Herramienta interna | React SPA (Vite) | Sin SEO, máxima interactividad |
| Landing page | Astro | Máximo rendimiento, mínimo JS |
| SaaS B2B | Next.js App Router | SSR + API Routes + auth |
