# Entorno de Desarrollo — Hardware, Herramientas e IA

## Tabla de contenidos
1. [Hardware recomendado](#hardware)
2. [Sistema operativo](#sistema-operativo)
3. [Editor y terminal](#editor-y-terminal)
4. [Herramientas esenciales](#herramientas-esenciales)
5. [IA como copiloto — modelo de confianza progresiva](#ia-como-copiloto)
6. [Setup inicial de proyecto](#setup-inicial)

---

## Hardware

### Especificaciones mínimas para desarrollo 2025-2026

| Componente | Mínimo | Recomendado | Justificación |
|---|---|---|---|
| **CPU** | Core i5 / Ryzen 5 | Core i7 / Ryzen 7 / Apple M3+ | Compilación, Docker, múltiples procesos |
| **RAM** | 16 GB | 32 GB | IDE + browser + DB + Docker simultáneos |
| **Storage** | 512 GB SSD NVMe | 1 TB SSD NVMe | Deps de npm, modelos de IA, DBs locales |
| **GPU** | Integrada | NVIDIA RTX (si ML/AI local) | Solo necesaria para entrenamiento local |

### Equipos recomendados por presupuesto

**Alto (~$1500+ USD)**:
- MacBook Pro M3/M4 — Ecosistema Apple, batería, rendimiento ARM
- Dell XPS 16 — Máximo rendimiento Windows, RTX 5000 opcional

**Medio (~$800-1200 USD)**:
- Lenovo ThinkPad X1 Carbon Gen 13 — Ultraligero, teclado premium
- MacBook Air M3 — Excelente relación rendimiento/portabilidad

**Estudiante (~$400-700 USD)**:
- Acer Aspire 5 / Lenovo ThinkPad E14 — Fiables, gama media
- Cualquier laptop con 16GB RAM + SSD NVMe es viable

### Regla clave
No comprar HDD mecánico. No comprar menos de 16 GB RAM. El SSD NVMe
afecta directamente la velocidad de `npm install`, builds y Docker.

---

## Sistema operativo

| OS | Mejor para | Notas |
|---|---|---|
| **macOS** | Dev iOS/macOS, fullstack general | Terminal Unix nativa, Homebrew |
| **Linux (Ubuntu)** | Backend, cloud, ML/AI, servidores | GPU NVIDIA nativa, Docker nativo |
| **Windows + WSL2** | Desarrollo general, gaming + dev | WSL2 da Linux real dentro de Windows |

Para Windows, instalar WSL2 es obligatorio para tener un entorno Unix
compatible con herramientas de desarrollo modernas.

---

## Editor y terminal

### VS Code (estándar de la industria)
Extensiones esenciales:
- **ESLint** — Linting de código
- **Prettier** — Formateo automático
- **TypeScript** — Soporte nativo (built-in)
- **Prisma** — Syntax highlighting para schema.prisma
- **Tailwind CSS IntelliSense** — Autocompletado de clases
- **GitLens** — Git avanzado integrado
- **Error Lens** — Errores inline en el editor

### Terminales recomendadas
- **Warp** — Terminal con IA integrada, autocompletado
- **iTerm2** (macOS) — Terminal avanzada clásica
- **Windows Terminal** — Para WSL2, tabs, personalización
- **Alacritty** — Ultra-rápida, GPU-accelerated

### Claude Code (terminal inteligente)
Herramienta de Anthropic para desarrollo asistido por IA desde terminal.
Permite delegar tareas de codificación completas a Claude con contexto
del repositorio completo.

---

## Herramientas esenciales

### Control de versiones
```bash
# Git — obligatorio
git --version
# GitHub CLI — gestión de repos desde terminal
gh --version
```

### Runtime y package manager
```bash
# Node.js 22 LTS (recomendado)
node --version  # v22.x
# npm (incluido) o pnpm (más rápido)
pnpm --version
```

### Contenedores
```bash
# Docker Desktop (o Orbstack en macOS)
docker --version
docker compose version
```

### Base de datos local
```bash
# PostgreSQL local via Docker
docker run -d --name postgres \
  -e POSTGRES_PASSWORD=devpass \
  -p 5432:5432 \
  postgres:17

# O usar Neon (serverless, sin instalación local)
```

---

## IA como copiloto

### El modelo de confianza progresiva

El 84% de devs usa IA, pero solo 29% confía en la precisión (2025).
El 66% se frustra con código "casi correcto".

**Nivel 1 — Delegable sin revisión profunda**:
- Boilerplate y scaffolding de archivos
- Documentación de código (JSDoc, README)
- Mensajes de commit
- Regex y one-liners

**Nivel 2 — Delegable con revisión**:
- Tests unitarios (verificar que cubran edge cases)
- Refactoring de código existente
- Queries SQL simples
- Componentes UI estándar

**Nivel 3 — Supervisión obligatoria**:
- Lógica de negocio compleja
- Autenticación y autorización
- Migraciones de base de datos
- Manejo de transacciones financieras
- Cualquier código que toque datos sensibles

**Nivel 4 — NO delegar a IA**:
- Decisiones de arquitectura sin entender las implicaciones
- Código de seguridad crítico sin comprensión del modelo de amenazas
- Evaluación de si una solución es correcta (circularidad)

### Herramientas de IA para dev

| Herramienta | Tipo | Costo | Mejor para |
|---|---|---|---|
| **Claude Code** | Terminal agent | API/Pro/Max | Tareas complejas multi-archivo |
| **GitHub Copilot** | Inline completion | $10-19/mes | Autocompletado rápido en editor |
| **Cursor** | IDE con IA | $20/mes | Edición con contexto de repo |
| **Claude.ai** | Chat | Free/Pro/Max | Arquitectura, diseño, análisis |

---

## Setup inicial

### Proyecto Next.js + TypeScript + Tailwind
```bash
# 1. Crear proyecto
npx create-next-app@latest my-project \
  --typescript --tailwind --eslint --app --src-dir

# 2. Instalar dependencias core
cd my-project
npm install zod @upstash/ratelimit @upstash/redis

# 3. ORM (elegir uno)
# Prisma:
npm install prisma @prisma/client
npx prisma init

# Drizzle:
npm install drizzle-orm postgres
npm install -D drizzle-kit

# 4. Auth (elegir uno)
# Clerk:
npm install @clerk/nextjs

# Better Auth:
npm install better-auth

# 5. Email
npm install resend

# 6. Testing
npm install -D vitest @testing-library/react @testing-library/jest-dom
npx playwright install

# 7. Crear .env.local
cat > .env.local << 'EOF'
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb
RESEND_API_KEY=re_xxx
NEXT_PUBLIC_TURNSTILE_SITE_KEY=0x...
TURNSTILE_SECRET_KEY=0x...
# Auth keys según la opción elegida
EOF

# 8. Gitignore
echo ".env.local" >> .gitignore
echo "node_modules" >> .gitignore
```

### Estructura recomendada
```
src/
├── app/                    ← Next.js App Router
│   ├── layout.tsx
│   ├── page.tsx
│   ├── api/
│   │   └── webhook/route.ts
│   ├── (auth)/
│   │   ├── login/page.tsx
│   │   └── register/page.tsx
│   └── dashboard/
│       ├── layout.tsx
│       └── page.tsx
├── components/             ← Componentes reutilizables
│   ├── ui/                 ← Componentes base (shadcn/ui)
│   └── forms/              ← Formularios específicos
├── lib/                    ← Lógica de negocio
│   ├── db.ts               ← Cliente de DB
│   ├── auth.ts             ← Configuración de auth
│   ├── email.ts            ← Cliente de Resend
│   ├── turnstile.ts        ← Validación CAPTCHA
│   └── rate-limit.ts       ← Rate limiting
├── schemas/                ← Esquemas Zod
│   └── contact.ts
└── tests/                  ← Tests unitarios
    ├── setup.ts
    └── lib/
        └── pricing.test.ts
e2e/                        ← Tests E2E (Playwright)
├── login.spec.ts
└── contact.spec.ts
```
