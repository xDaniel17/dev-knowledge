---
title: "Patrones de Diseño Creacionales — Factory, Builder, Singleton"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - patrones-de-diseno
  - creacionales
  - factory
  - builder
  - singleton
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Patrones de Diseño - Estructurales]]"
  - "[[Patrones de Diseño - Comportamiento]]"
  - "[[Principios SOLID]]"
  - "[[Composición]]"
  - "[[Estructura de proyecto]]"
---

# Patrones de Diseño Creacionales — Factory, Builder, Singleton

Los patrones creacionales resuelven cómo crear objetos de forma flexible y desacoplada. En lugar de usar `new MiClase()` directamente (que acopla al nombre concreto), estos patrones abstraen la creación para que el código que usa el objeto no sepa ni le importe cómo fue construido.

---

## Factory Method

Define una interfaz para crear un objeto, pero deja que las subclases (o una función centralizada) decidan qué clase concreta instanciar.

### El problema

```typescript
// Sin factory — el código cliente está acoplado a cada implementación
function createNotification(type: string, message: string) {
  if (type === 'email') return new EmailNotification(message);
  if (type === 'push') return new PushNotification(message);
  if (type === 'sms') return new SMSNotification(message);
  // Agregar 'slack' requiere modificar aquí — viola OCP
}
```

### Con Factory Method

```typescript
// Interfaz común
interface Notification {
  send(to: string): Promise<void>;
}

// Implementaciones concretas
class EmailNotification implements Notification {
  constructor(private message: string) {}
  async send(to: string) {
    await mailer.send({ to, body: this.message });
  }
}

class PushNotification implements Notification {
  constructor(private message: string) {}
  async send(to: string) {
    await pushService.notify({ userId: to, body: this.message });
  }
}

// Factory — un único lugar de creación
type NotificationType = 'email' | 'push' | 'sms';

const notificationFactories: Record<NotificationType, (msg: string) => Notification> = {
  email: (msg) => new EmailNotification(msg),
  push:  (msg) => new PushNotification(msg),
  sms:   (msg) => new SMSNotification(msg),
};

function createNotification(type: NotificationType, message: string): Notification {
  const factory = notificationFactories[type];
  if (!factory) throw new Error(`Unknown notification type: ${type}`);
  return factory(message);
}

// Cliente — no sabe qué clase concreta se crea
const notif = createNotification('email', 'Tu pedido fue enviado');
await notif.send('daniel@example.com');
```

**En Strapi:** `createCoreController()` y `createCoreRouter()` son factories que generan controladores y routers con comportamiento base, dejándote extender solo lo que necesitas.

---

## Abstract Factory

Crea familias de objetos relacionados sin especificar sus clases concretas. Útil cuando necesitas garantizar que los objetos creados sean compatibles entre sí.

```typescript
// Familia: componentes de UI para diferentes temas
interface Button { render(): string; }
interface Input  { render(): string; }

// Familia Light
class LightButton implements Button { render() { return '<button class="light">'; } }
class LightInput  implements Input  { render() { return '<input class="light">'; } }

// Familia Dark
class DarkButton implements Button { render() { return '<button class="dark">'; } }
class DarkInput  implements Input  { render() { return '<input class="dark">'; } }

// Abstract Factory — garantiza consistencia dentro de la familia
interface UIFactory {
  createButton(): Button;
  createInput(): Input;
}

class LightThemeFactory implements UIFactory {
  createButton() { return new LightButton(); }
  createInput()  { return new LightInput();  }
}

class DarkThemeFactory implements UIFactory {
  createButton() { return new DarkButton(); }
  createInput()  { return new DarkInput();  }
}

// El formulario no sabe qué tema usa — solo usa la factory
function renderForm(factory: UIFactory) {
  const btn = factory.createButton();
  const inp = factory.createInput();
  return `${inp.render()} ${btn.render()}`;
}

const theme = userPrefers === 'dark' ? new DarkThemeFactory() : new LightThemeFactory();
renderForm(theme);
```

---

## Builder

Construye objetos complejos paso a paso. Separa la construcción de la representación, permitiendo crear diferentes variantes con el mismo proceso.

### El problema

```typescript
// Constructor con demasiados parámetros — difícil de usar y extender
const query = new DatabaseQuery(
  'users',           // tabla
  ['id', 'name'],    // columnas
  { active: true },  // filtros
  'created_at',      // orden
  'DESC',            // dirección
  10,                // límite
  0,                 // offset
  true,              // incluir eliminados
);
```

### Con Builder

```typescript
class QueryBuilder {
  private table = '';
  private columns: string[] = ['*'];
  private conditions: Record<string, unknown> = {};
  private orderBy: string | null = null;
  private orderDir: 'ASC' | 'DESC' = 'ASC';
  private limitValue: number | null = null;
  private offsetValue = 0;

  from(table: string) {
    this.table = table;
    return this; // fluent interface — permite encadenar
  }

  select(...cols: string[]) {
    this.columns = cols;
    return this;
  }

  where(conditions: Record<string, unknown>) {
    this.conditions = { ...this.conditions, ...conditions };
    return this;
  }

  order(column: string, dir: 'ASC' | 'DESC' = 'ASC') {
    this.orderBy = column;
    this.orderDir = dir;
    return this;
  }

  limit(n: number) {
    this.limitValue = n;
    return this;
  }

  offset(n: number) {
    this.offsetValue = n;
    return this;
  }

  build(): CompiledQuery {
    if (!this.table) throw new Error('Table is required');
    return { table: this.table, columns: this.columns, /* ... */ };
  }
}

// Uso — legible y explícito
const query = new QueryBuilder()
  .from('users')
  .select('id', 'name', 'email')
  .where({ active: true, role: 'admin' })
  .order('created_at', 'DESC')
  .limit(10)
  .offset(20)
  .build();
```

**En el stack:** el encadenamiento fluent de Prisma (`prisma.user.findMany({ where: {}, orderBy: {}, take: 10 })`) sigue el espíritu del Builder. Los Server Actions de Next.js con `revalidateTag` + `revalidatePath` también se asemejan a un builder de efectos.

---

## Singleton

Garantiza que una clase tenga exactamente una instancia y provee un punto de acceso global a ella.

```typescript
// Singleton para la conexión a BD — crear múltiples conexiones es costoso
class DatabaseConnection {
  private static instance: DatabaseConnection | null = null;
  private client: PrismaClient;

  private constructor() {
    // privado — nadie puede hacer `new DatabaseConnection()`
    this.client = new PrismaClient();
  }

  static getInstance(): DatabaseConnection {
    if (!DatabaseConnection.instance) {
      DatabaseConnection.instance = new DatabaseConnection();
    }
    return DatabaseConnection.instance;
  }

  getClient(): PrismaClient {
    return this.client;
  }
}

// Siempre la misma instancia
const db1 = DatabaseConnection.getInstance();
const db2 = DatabaseConnection.getInstance();
console.log(db1 === db2); // true
```

**En módulos ES / Node:** la forma idiomática de Singleton es simplemente exportar una instancia desde un módulo. Node cachea los módulos, por lo que la instancia se comparte:

```typescript
// db.ts — esto ES un singleton en Node.js
import { PrismaClient } from '@prisma/client';

// En desarrollo, evitar múltiples instancias por hot reload
const globalForPrisma = global as unknown as { prisma: PrismaClient };
export const prisma = globalForPrisma.prisma ?? new PrismaClient();
if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

> [!WARNING]
> El Singleton clásico con estado global hace el código difícil de testear porque los tests comparten estado. Prefiere la inyección de dependencias sobre el acceso global directo. El Singleton es aceptable para recursos que genuinamente deben ser únicos: conexión a BD, logger, configuración.

---

## Prototype

Crea nuevos objetos clonando una instancia existente en lugar de construir desde cero.

```typescript
interface Cloneable<T> {
  clone(): T;
}

class UserConfig implements Cloneable<UserConfig> {
  constructor(
    public theme: 'light' | 'dark',
    public language: string,
    public notifications: { email: boolean; push: boolean },
  ) {}

  clone(): UserConfig {
    return new UserConfig(
      this.theme,
      this.language,
      { ...this.notifications }, // copia profunda de objetos anidados
    );
  }
}

// Configuración base que se clona para personalizar
const defaultConfig = new UserConfig('light', 'es', { email: true, push: false });

const danielConfig = defaultConfig.clone();
danielConfig.theme = 'dark';
// defaultConfig sigue siendo 'light'
```

**En JavaScript nativo:** `Object.assign({}, original)` y el spread `{ ...original }` son formas de Prototype shallow. Para copias profundas: `structuredClone(original)` (nativo en Node 17+).

---

## Cuándo usar cada uno

| Patrón | Usar cuando... |
|--------|----------------|
| **Factory Method** | Necesitas crear objetos de distintos tipos basado en una condición, sin acoplar al tipo concreto |
| **Abstract Factory** | Tienes familias de objetos relacionados que deben ser consistentes entre sí |
| **Builder** | El objeto tiene muchos parámetros opcionales o su construcción requiere múltiples pasos |
| **Singleton** | Un recurso costoso debe existir una sola vez (conexión BD, logger, configuración global) |
| **Prototype** | Clonar un objeto existente es más barato o conveniente que construir uno nuevo |

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
