---
title: "Principios SOLID — Guía de diseño orientado a objetos"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - solid
  - principios
  - oop
  - diseño
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Separación de Responsabilidades]]"
  - "[[Composición]]"
  - "[[Middlewares]]"
  - "[[Estructura de proyecto]]"
---

# Principios SOLID — Guía de diseño orientado a objetos

SOLID es un acrónimo de cinco principios de diseño que hacen el código más mantenible, extensible y testeable. Formulados por Robert C. Martin ("Uncle Bob"), se aplican principalmente a OOP pero muchos tienen equivalentes directos en programación funcional y arquitecturas modernas.

---

## S — Single Responsibility Principle (SRP)

> Una clase (o módulo) debe tener una sola razón para cambiar.

En otras palabras: una unidad de código debe hacer una sola cosa. Si tienes que describir tu clase con "y también...", probablemente viola SRP.

```typescript
// ❌ Viola SRP — tres responsabilidades en una clase
class UserService {
  async getUser(id: string) { /* consultar BD */ }
  sendWelcomeEmail(email: string) { /* enviar email */ }
  generatePDFReport(userId: string) { /* generar PDF */ }
}

// ✅ Cada clase tiene una responsabilidad
class UserRepository {
  async findById(id: string): Promise<User | null> {
    return db.user.findUnique({ where: { id } });
  }
}

class EmailService {
  async sendWelcome(email: string): Promise<void> {
    await mailer.send({ to: email, template: 'welcome' });
  }
}

class ReportService {
  async generateUserReport(userId: string): Promise<Buffer> {
    // lógica de generación de PDF
  }
}
```

**En Express:** cada middleware hace una cosa (auth, logging, rate-limit). Eso es SRP aplicado a pipelines.

---

## O — Open/Closed Principle (OCP)

> Las entidades de software deben estar abiertas para extensión y cerradas para modificación.

Añadir nueva funcionalidad no debería requerir modificar el código existente — solo extenderlo.

```typescript
// ❌ Viola OCP — hay que modificar esta función cada vez que se agrega un tipo
function calculateDiscount(user: User): number {
  if (user.type === 'premium') return 0.2;
  if (user.type === 'vip') return 0.35;
  // Agregar 'superVip' requiere modificar aquí
  return 0;
}

// ✅ Respeta OCP — nueva lógica se agrega como nueva clase, sin tocar las existentes
interface DiscountStrategy {
  calculate(user: User): number;
}

class PremiumDiscount implements DiscountStrategy {
  calculate() { return 0.2; }
}

class VIPDiscount implements DiscountStrategy {
  calculate() { return 0.35; }
}

// Para agregar 'superVip': crear SuperVIPDiscount, no modificar nada
const strategies: Record<string, DiscountStrategy> = {
  premium: new PremiumDiscount(),
  vip: new VIPDiscount(),
};

function calculateDiscount(user: User): number {
  return strategies[user.type]?.calculate(user) ?? 0;
}
```

---

## L — Liskov Substitution Principle (LSP)

> Si `S` es un subtipo de `T`, los objetos de tipo `T` pueden reemplazarse por objetos de tipo `S` sin alterar el comportamiento correcto del programa.

En términos prácticos: una clase que extiende a otra debe poder usarse en su lugar sin sorpresas.

```typescript
// ❌ Viola LSP — la subclase rompe el contrato de la base
class Rectangle {
  setWidth(w: number) { this.width = w; }
  setHeight(h: number) { this.height = h; }
  area() { return this.width * this.height; }
}

class Square extends Rectangle {
  setWidth(w: number) {
    this.width = w;
    this.height = w; // un cuadrado fuerza ambas dimensiones iguales
  }
  // setHeight hace lo mismo — rompe el contrato de Rectangle
}

function resize(rect: Rectangle) {
  rect.setWidth(5);
  rect.setHeight(10);
  console.log(rect.area()); // Esperado: 50, pero Square da 100
}

// ✅ Respetar LSP — no forzar una jerarquía que no tiene sentido
interface Shape {
  area(): number;
}

class Rectangle implements Shape { ... }
class Square implements Shape { ... }
```

---

## I — Interface Segregation Principle (ISP)

> Los clientes no deberían verse forzados a depender de interfaces que no usan.

Mejor muchas interfaces pequeñas que una grande.

```typescript
// ❌ Interface "gorda" — no todos los usuarios necesitan todo
interface UserActions {
  read(): void;
  write(): void;
  delete(): void;
  manageRoles(): void;
  viewAuditLog(): void;
}

// ✅ Interfaces segregadas
interface Reader {
  read(): void;
}

interface Writer extends Reader {
  write(): void;
  delete(): void;
}

interface Admin extends Writer {
  manageRoles(): void;
  viewAuditLog(): void;
}

// Cada clase implementa solo lo que necesita
class ReadOnlyUser implements Reader { ... }
class EditorUser implements Writer { ... }
class AdminUser implements Admin { ... }
```

**En TypeScript/React:** los Props de los componentes son interfaces. Divide props grandes en grupos con sentido en lugar de pasar 15 props opcionales.

---

## D — Dependency Inversion Principle (DIP)

> Los módulos de alto nivel no deberían depender de módulos de bajo nivel. Ambos deberían depender de abstracciones.

```typescript
// ❌ Alto nivel (UserService) depende directamente del bajo nivel (Prisma)
class UserService {
  async getUser(id: string) {
    return prisma.user.findUnique({ where: { id } }); // acoplado a Prisma
  }
}

// ✅ DIP — ambos dependen de una abstracción (interface)
interface UserRepository {
  findById(id: string): Promise<User | null>;
  save(user: User): Promise<User>;
}

// Implementación concreta — puede cambiarse sin tocar UserService
class PrismaUserRepository implements UserRepository {
  async findById(id: string) {
    return prisma.user.findUnique({ where: { id } });
  }
  async save(user: User) {
    return prisma.user.upsert({ ... });
  }
}

// Alto nivel depende de la abstracción, no de Prisma
class UserService {
  constructor(private repository: UserRepository) {} // inyección de dependencia

  async getUser(id: string) {
    return this.repository.findById(id);
  }
}

// En tests puedes inyectar un mock sin cambiar UserService
const mockRepo: UserRepository = { findById: async () => fakeUser, save: async () => fakeUser };
const service = new UserService(mockRepo);
```

**DIP en arquitecturas modernas:** Express inyecta dependencias a través de middleware; Next.js inyecta el contexto de request en Server Components; Angular tiene un sistema de DI nativo.

---

## ¿Cuándo aplicar SOLID?

> [!TIP]
> SOLID no es una lista de obligaciones — es una guía para detectar problemas. Antes de aplicar cualquier principio, pregúntate: **¿este código me está causando dolor?** Si es difícil de testear, extender o entender, SOLID ayuda a diagnosticar por qué.

No todas las funciones necesitan interfaces segregadas. No todos los módulos necesitan Dependency Inversion. El dogmatismo en SOLID genera over-engineering. Aplica cuando el código crece y empieza a doler.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
