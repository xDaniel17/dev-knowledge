---
title: "Patrones de Diseño Estructurales — Adapter, Decorator, Facade, Proxy"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - patrones-de-diseno
  - estructurales
  - adapter
  - decorator
  - facade
  - proxy
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Patrones de Diseño - Creacionales]]"
  - "[[Patrones de Diseño - Comportamiento]]"
  - "[[Composición]]"
  - "[[Middlewares]]"
  - "[[Principios SOLID]]"
---

# Patrones de Diseño Estructurales — Adapter, Decorator, Facade, Proxy

Los patrones estructurales resuelven cómo componer clases y objetos en estructuras más grandes. Se centran en las relaciones entre entidades: cómo conectar interfaces incompatibles, añadir responsabilidades sin modificar código existente, o simplificar acceso a sistemas complejos.

---

## Adapter

Convierte la interfaz de una clase en otra que el cliente espera. Permite que objetos con interfaces incompatibles trabajen juntos.

### El problema

```typescript
// Tienes un sistema nuevo que espera esta interfaz
interface PaymentGateway {
  charge(amountCents: number, currency: string, token: string): Promise<{ id: string }>;
}

// Pero tu proveedor legacy tiene esta API diferente
class LegacyStripeSDK {
  makePayment(opts: {
    amount: number; // en dólares, no centavos
    curr: string;
    card_token: string;
  }): Promise<{ payment_id: string }> { ... }
}
```

### Con Adapter

```typescript
// Adapter — envuelve el legacy y expone la interfaz nueva
class StripeAdapter implements PaymentGateway {
  constructor(private legacy: LegacyStripeSDK) {}

  async charge(amountCents: number, currency: string, token: string) {
    const result = await this.legacy.makePayment({
      amount: amountCents / 100,  // adaptar: centavos → dólares
      curr: currency,
      card_token: token,          // adaptar: renombrar campo
    });
    return { id: result.payment_id }; // adaptar: normalizar respuesta
  }
}

// El cliente usa la interfaz estándar, sin saber que hay un legacy debajo
const gateway: PaymentGateway = new StripeAdapter(new LegacyStripeSDK());
await gateway.charge(1999, 'USD', 'tok_abc123');
```

**En el stack:** los repositorios en Clean Architecture actúan como Adapters entre el dominio y el ORM. Los adaptadores de Next.js para Vercel/Netlify/Cloudflare adaptan las APIs de despliegue al modelo de Next.js.

---

## Decorator

Añade responsabilidades a un objeto dinámicamente, sin modificarlo ni usar herencia. Envuelve el objeto con otro que extiende su comportamiento.

```typescript
// Interfaz base
interface Logger {
  log(message: string): void;
}

// Implementación base
class ConsoleLogger implements Logger {
  log(message: string) {
    console.log(message);
  }
}

// Decorator base — mantiene la referencia al objeto envuelto
abstract class LoggerDecorator implements Logger {
  constructor(protected wrapped: Logger) {}
  abstract log(message: string): void;
}

// Decorator concreto 1 — añade timestamps
class TimestampLogger extends LoggerDecorator {
  log(message: string) {
    this.wrapped.log(`[${new Date().toISOString()}] ${message}`);
  }
}

// Decorator concreto 2 — añade prefijo de nivel
class LevelLogger extends LoggerDecorator {
  constructor(wrapped: Logger, private level: 'INFO' | 'WARN' | 'ERROR') {
    super(wrapped);
  }
  log(message: string) {
    this.wrapped.log(`[${this.level}] ${message}`);
  }
}

// Componer decorators — el orden importa
const logger = new TimestampLogger(
  new LevelLogger(
    new ConsoleLogger(),
    'INFO',
  ),
);

logger.log('Usuario creado');
// Output: [2026-03-30T10:00:00.000Z] [INFO] Usuario creado
```

**Decorator en Express:** cada middleware es un decorator funcional sobre el ciclo `req/res`. `withAuth(withRateLimit(handler))` decora el handler con capas adicionales.

**Decorator en TypeScript/Angular:** los decoradores de TypeScript (`@Component`, `@Injectable`, `@Input`) son la implementación nativa del patrón. Añaden metadatos y comportamiento sin modificar la clase base.

```typescript
// Decorator de TypeScript para memoización
function memoize(target: any, key: string, descriptor: PropertyDescriptor) {
  const original = descriptor.value;
  const cache = new Map();

  descriptor.value = function (...args: any[]) {
    const cacheKey = JSON.stringify(args);
    if (cache.has(cacheKey)) return cache.get(cacheKey);
    const result = original.apply(this, args);
    cache.set(cacheKey, result);
    return result;
  };

  return descriptor;
}

class MathService {
  @memoize
  fibonacci(n: number): number {
    if (n <= 1) return n;
    return this.fibonacci(n - 1) + this.fibonacci(n - 2);
  }
}
```

---

## Facade

Proporciona una interfaz simplificada a un subsistema complejo. El cliente interactúa con la Facade y no necesita conocer los detalles internos.

```typescript
// Subsistema complejo — múltiples servicios con APIs diferentes
class AuthService {
  async validateToken(token: string): Promise<{ userId: string }> { ... }
}

class UserService {
  async getProfile(userId: string): Promise<UserProfile> { ... }
}

class PermissionService {
  async getPermissions(userId: string): Promise<string[]> { ... }
}

class SessionService {
  async createSession(userId: string): Promise<string> { ... }
}

// Sin Facade — el cliente debe orquestar todo esto
async function loginHandler(req: Request, res: Response) {
  const { userId } = await authService.validateToken(req.headers.authorization!);
  const profile = await userService.getProfile(userId);
  const permissions = await permissionService.getPermissions(userId);
  const sessionId = await sessionService.createSession(userId);
  res.json({ profile, permissions, sessionId });
}

// ✅ Con Facade — interfaz simple sobre el subsistema
class AuthFacade {
  constructor(
    private auth: AuthService,
    private users: UserService,
    private permissions: PermissionService,
    private sessions: SessionService,
  ) {}

  async loginWithToken(token: string) {
    const { userId } = await this.auth.validateToken(token);

    const [profile, permissions, sessionId] = await Promise.all([
      this.users.getProfile(userId),
      this.permissions.getPermissions(userId),
      this.sessions.createSession(userId),
    ]);

    return { profile, permissions, sessionId };
  }
}

// Handler limpio — delega todo a la Facade
async function loginHandler(req: Request, res: Response) {
  const result = await authFacade.loginWithToken(req.headers.authorization!);
  res.json(result);
}
```

**Facade vs Adapter:** el Adapter cambia la interfaz de algo existente. La Facade simplifica el acceso a algo complejo. Son complementarios.

---

## Proxy

Provee un sustituto o representante de otro objeto para controlar el acceso a él. Útil para caché, control de acceso, logging, lazy loading.

```typescript
interface UserRepository {
  findById(id: string): Promise<User | null>;
}

// Implementación real
class DatabaseUserRepository implements UserRepository {
  async findById(id: string): Promise<User | null> {
    return prisma.user.findUnique({ where: { id } });
  }
}

// Proxy de caché — misma interfaz, añade caching transparente
class CachedUserRepository implements UserRepository {
  private cache = new Map<string, User>();

  constructor(private real: UserRepository) {}

  async findById(id: string): Promise<User | null> {
    if (this.cache.has(id)) {
      console.log('Cache hit:', id);
      return this.cache.get(id)!;
    }

    const user = await this.real.findById(id);
    if (user) this.cache.set(id, user);
    return user;
  }
}

// El cliente no sabe si habla con la BD o con el proxy
const repo: UserRepository = new CachedUserRepository(new DatabaseUserRepository());
await repo.findById('123'); // BD
await repo.findById('123'); // caché
```

**Proxy con JavaScript `Proxy`:**

```typescript
// Proxy nativo para validación de acceso
function createReadOnlyProxy<T extends object>(target: T): Readonly<T> {
  return new Proxy(target, {
    set(_, prop) {
      throw new Error(`Cannot set property "${String(prop)}" — object is read-only`);
    },
    deleteProperty(_, prop) {
      throw new Error(`Cannot delete property "${String(prop)}" — object is read-only`);
    },
  });
}

const config = createReadOnlyProxy({ apiUrl: 'https://api.example.com', timeout: 5000 });
config.timeout = 3000; // ❌ Error: Cannot set property "timeout"
```

---

## Composite

Compone objetos en estructuras de árbol para representar jerarquías parte-todo. Permite tratar objetos individuales y composiciones de forma uniforme.

```typescript
// Árbol de menú de navegación
interface MenuItem {
  render(depth: number): string;
}

// Hoja — elemento sin hijos
class MenuLink implements MenuItem {
  constructor(private label: string, private href: string) {}

  render(depth: number) {
    return `${'  '.repeat(depth)}<a href="${this.href}">${this.label}</a>`;
  }
}

// Composición — puede contener otros elementos (hojas o composiciones)
class MenuGroup implements MenuItem {
  private children: MenuItem[] = [];

  constructor(private label: string) {}

  add(item: MenuItem) {
    this.children.push(item);
    return this;
  }

  render(depth: number): string {
    const childrenHtml = this.children.map(c => c.render(depth + 1)).join('\n');
    return `${'  '.repeat(depth)}<details><summary>${this.label}</summary>\n${childrenHtml}\n${'  '.repeat(depth)}</details>`;
  }
}

// Construir la jerarquía
const nav = new MenuGroup('Navegación')
  .add(new MenuLink('Inicio', '/'))
  .add(
    new MenuGroup('Productos')
      .add(new MenuLink('Todos', '/products'))
      .add(new MenuLink('Ofertas', '/products/sale'))
  )
  .add(new MenuLink('Contacto', '/contact'));

console.log(nav.render(0));
```

**En React:** el árbol de componentes es el patrón Composite. Cada componente puede renderizar otros componentes (composición) o elementos DOM (hojas) con la misma interfaz JSX.

---

## Cuándo usar cada uno

| Patrón | Usar cuando... |
|--------|----------------|
| **Adapter** | Integrar una librería externa o legacy con una interfaz incompatible con la tuya |
| **Decorator** | Añadir comportamiento (logging, caché, auth) sin modificar la clase original |
| **Facade** | Simplificar el acceso a un subsistema complejo con múltiples piezas |
| **Proxy** | Controlar el acceso a un objeto: caché, lazy loading, validación, logging |
| **Composite** | Representar jerarquías donde hojas y grupos se tratan uniformemente |

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
