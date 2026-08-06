# Testing y Calidad de Código — Vitest, Playwright, Clean Code

## Tabla de contenidos
1. [Pirámide de tests](#pirámide-de-tests)
2. [Tests unitarios con Vitest](#vitest)
3. [Tests E2E con Playwright](#playwright)
4. [Principios de Clean Code](#clean-code)
5. [SOLID en TypeScript](#solid)
6. [CI/CD pipeline](#cicd)

---

## Pirámide de tests

```
            ╱╲
           ╱  ╲           E2E (Playwright)
          ╱ E2E╲          → Flujos completos de usuario
         ╱──────╲         → Login, checkout, forms
        ╱        ╲
       ╱Integration╲     Integración
      ╱──────────────╲   → API Routes + DB
     ╱                ╲   → Server Actions
    ╱    Unit Tests     ╲ Unitarios (Vitest)
   ╱────────────────────╲ → Funciones puras
  ╱                      ╲→ Lógica de negocio
 ╱________________________╲→ Componentes aislados
```

**Distribución recomendada**: 70% unitarios, 20% integración, 10% E2E.
Los unitarios son rápidos y baratos; los E2E son lentos pero validan
flujos reales completos.

---

## Vitest

Vitest es el runner de tests recomendado para proyectos con Vite/Next.js.
Reemplaza a Jest con compatibilidad total y mejor rendimiento.

### Setup
```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';
import react from '@vitejs/plugin-react';
import path from 'path';

export default defineConfig({
  plugins: [react()],
  test: {
    environment: 'jsdom', // Para componentes React
    globals: true,
    setupFiles: ['./tests/setup.ts'],
  },
  resolve: {
    alias: { '@': path.resolve(__dirname, './src') },
  },
});
```

### Test unitario — función pura
```typescript
// lib/pricing.ts
export function calculateTotal(
  basePrice: number,
  quantity: number,
  discount: number = 0
): number {
  if (quantity < 0) throw new Error('Quantity must be positive');
  if (discount < 0 || discount > 100) throw new Error('Invalid discount');
  return basePrice * quantity * (1 - discount / 100);
}

// lib/pricing.test.ts
import { describe, it, expect } from 'vitest';
import { calculateTotal } from './pricing';

describe('calculateTotal', () => {
  it('calcula precio sin descuento', () => {
    expect(calculateTotal(100, 3)).toBe(300);
  });

  it('aplica descuento correctamente', () => {
    expect(calculateTotal(100, 2, 10)).toBe(180);
  });

  it('lanza error con cantidad negativa', () => {
    expect(() => calculateTotal(100, -1)).toThrow('Quantity must be positive');
  });

  it('lanza error con descuento inválido', () => {
    expect(() => calculateTotal(100, 1, 150)).toThrow('Invalid discount');
  });

  it('retorna 0 con cantidad 0', () => {
    expect(calculateTotal(100, 0)).toBe(0);
  });
});
```

### Test de componente con mock
```typescript
// components/UserCard.test.tsx
import { render, screen } from '@testing-library/react';
import { describe, it, expect, vi } from 'vitest';
import { UserCard } from './UserCard';

// Mock de módulo externo
vi.mock('@/lib/api', () => ({
  fetchUser: vi.fn().mockResolvedValue({ name: 'Camilo', email: 'c@test.com' }),
}));

describe('UserCard', () => {
  it('muestra nombre del usuario', async () => {
    render(<UserCard userId="123" />);
    expect(await screen.findByText('Camilo')).toBeDefined();
  });
});
```

### Snapshot testing
```typescript
it('renderiza correctamente', () => {
  const { container } = render(<PricingCard plan="starter" price={29} />);
  expect(container).toMatchSnapshot();
});
```

---

## Playwright

Playwright simula usuarios reales en navegadores (Chromium, Firefox, WebKit)
con tasa de falsos positivos menor que Cypress o Selenium.

### Setup
```bash
npm init playwright@latest
# Instala browsers y genera playwright.config.ts
```

### Test E2E — Flujo de login
```typescript
// e2e/login.spec.ts
import { test, expect } from '@playwright/test';

test.describe('Authentication flow', () => {
  test('usuario puede hacer login exitosamente', async ({ page }) => {
    await page.goto('/login');
    
    // Llenar formulario
    await page.fill('[name="email"]', 'test@example.com');
    await page.fill('[name="password"]', 'SecurePass123!');
    
    // Submit
    await page.click('button[type="submit"]');
    
    // Verificar redirección al dashboard
    await expect(page).toHaveURL('/dashboard');
    await expect(page.locator('h1')).toContainText('Dashboard');
  });

  test('muestra error con credenciales incorrectas', async ({ page }) => {
    await page.goto('/login');
    
    await page.fill('[name="email"]', 'wrong@example.com');
    await page.fill('[name="password"]', 'wrong');
    await page.click('button[type="submit"]');
    
    // Verificar mensaje de error
    await expect(page.locator('[role="alert"]')).toBeVisible();
  });
});
```

### Test E2E — Formulario con Turnstile
```typescript
test('contacto con CAPTCHA bypaseado en testing', async ({ page }) => {
  // En test env, Turnstile usa site key de testing que auto-pasa
  await page.goto('/contact');
  
  await page.fill('[name="name"]', 'Test User');
  await page.fill('[name="email"]', 'test@test.com');
  await page.fill('[name="message"]', 'This is a test message');
  
  // Esperar a que Turnstile se resuelva (test mode)
  await page.waitForSelector('[data-turnstile-success="true"]');
  
  await page.click('button[type="submit"]');
  await expect(page.locator('.success-message')).toBeVisible();
});
```

---

## Clean Code

Principios de Robert C. Martin aplicados a TypeScript moderno:

### 1. Nombres descriptivos
```typescript
// ❌ Nombres crípticos
const d = new Date();
const u = await getU(id);
const calc = (p, q, d) => p * q * (1 - d / 100);

// ✅ Nombres que revelan intención
const currentDate = new Date();
const user = await getUserById(userId);
const calculateDiscountedPrice = (price, quantity, discountPercent) =>
  price * quantity * (1 - discountPercent / 100);
```

### 2. Funciones pequeñas de propósito único
```typescript
// ❌ Función que hace demasiado
async function processOrder(data) {
  // valida, calcula, guarda en DB, envía email, actualiza stock...
  // 150 líneas
}

// ✅ Una responsabilidad por función
async function processOrder(data: OrderInput) {
  const validated = validateOrderInput(data);
  const total = calculateOrderTotal(validated.items);
  const order = await saveOrder({ ...validated, total });
  await updateInventory(validated.items);
  await sendOrderConfirmation(order);
  return order;
}
```

### 3. Separación de queries y comandos
```typescript
// ❌ Función que lee Y modifica (efecto secundario oculto)
function getAndIncrementCounter(): number {
  counter++;
  return counter;
}

// ✅ Separar lectura de escritura
function getCounter(): number { return counter; }
function incrementCounter(): void { counter++; }
```

### 4. Error handling en bloques aislados
```typescript
// ❌ Try-catch que engloba todo
try {
  const user = await getUser(id);
  const orders = await getOrders(user.id);
  const total = calculateTotal(orders);
  await sendReport(user.email, total);
} catch (e) {
  console.log('Something failed'); // ¿Qué falló exactamente?
}

// ✅ Manejo granular
async function generateUserReport(userId: string) {
  const user = await getUser(userId);
  if (!user) throw new UserNotFoundError(userId);

  const orders = await getOrders(user.id);
  const total = calculateTotal(orders);

  try {
    await sendReport(user.email, total);
  } catch (emailError) {
    // El email es secundario — loggear pero no fallar
    console.error('Report email failed:', { userId, error: emailError });
  }

  return { total, orderCount: orders.length };
}
```

---

## SOLID

### S — Single Responsibility
Cada clase/módulo tiene una sola razón para cambiar.

### O — Open/Closed
Abierto a extensión, cerrado a modificación (usar interfaces/tipos).

### L — Liskov Substitution
Los subtipos deben ser intercambiables con sus tipos base.

### I — Interface Segregation
Interfaces pequeñas y específicas, no una interfaz gigante.

### D — Dependency Inversion
Depender de abstracciones, no de implementaciones concretas.

```typescript
// ✅ Dependency Inversion con TypeScript

// Abstracción (interfaz)
interface EmailService {
  send(to: string, subject: string, body: string): Promise<void>;
}

// Implementación concreta A
class ResendEmailService implements EmailService {
  async send(to: string, subject: string, body: string) {
    await resend.emails.send({ to, subject, html: body, from: '...' });
  }
}

// Implementación concreta B (para tests)
class MockEmailService implements EmailService {
  sent: Array<{ to: string; subject: string }> = [];
  async send(to: string, subject: string, body: string) {
    this.sent.push({ to, subject }); // Solo registra, no envía
  }
}

// El servicio depende de la ABSTRACCIÓN, no de Resend directamente
class OrderService {
  constructor(private emailService: EmailService) {}

  async processOrder(order: Order) {
    // ... lógica
    await this.emailService.send(order.email, 'Confirmación', '...');
  }
}
```

---

## CI/CD

### GitHub Actions pipeline mínimo
```yaml
# .github/workflows/ci.yml
name: CI
on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22' }
      - run: npm ci
      - run: npm run lint
      - run: npm run type-check    # tsc --noEmit
      - run: npm run test          # vitest run
      
  e2e:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with: { node-version: '22' }
      - run: npm ci
      - run: npx playwright install --with-deps
      - run: npm run build
      - run: npm run test:e2e      # playwright test
```

### Vercel deploy automático
- Push a `main` → deploy a producción
- Push a branch → preview deployment
- PR → preview URL automática para revisión
