---
title: "Arquitecturas de Software — MVC, Clean, Hexagonal, Event-driven"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - arquitectura
  - mvc
  - clean-architecture
  - hexagonal
  - event-driven
  - microservicios
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Separación de Responsabilidades]]"
  - "[[Principios SOLID]]"
  - "[[Middlewares]]"
  - "[[Estructura de proyecto]]"
  - "[[App Router]]"
---

# Arquitecturas de Software — MVC, Clean, Hexagonal, Event-driven

Una arquitectura de software define cómo se organizan y comunican las partes de un sistema. Elegir la correcta depende del tamaño del equipo, la complejidad del dominio y cómo se espera que el sistema evolucione.

---

## MVC — Model View Controller

El patrón más conocido. Separa la aplicación en tres capas con responsabilidades claras:

```
Usuario
  │ acción
  ▼
Controller ──── delega ──→ Model ──── lee/escribe ──→ Base de Datos
  │                          │
  │ resultado                │ datos
  ▼                          ▼
View  ←────────────────── datos
```

| Capa | Responsabilidad |
|------|----------------|
| **Model** | Datos, reglas de negocio, acceso a BD |
| **View** | Presentación — HTML, JSON, o UI |
| **Controller** | Recibe peticiones, coordina Model y View |

```typescript
// Controller — coordina, no contiene lógica de negocio
export class UserController {
  constructor(private userModel: UserModel) {}

  async show(req: Request, res: Response) {
    const user = await this.userModel.findById(req.params.id);
    if (!user) return res.status(404).json({ error: 'Not found' });
    res.json(user); // View = JSON en una API REST
  }
}

// Model — datos + reglas
export class UserModel {
  async findById(id: string): Promise<User | null> {
    return db.user.findUnique({ where: { id } });
  }
}
```

**Cuándo usarlo:** proyectos medianos, frameworks que lo imponen (Rails, Laravel, Django, Express con estructura básica, Strapi).

**Limitación:** el Model tiende a crecer y mezclar acceso a datos con lógica de negocio compleja. Para dominios ricos, Clean Architecture escala mejor.

---

## Clean Architecture

Propuesta por Robert C. Martin. Organiza el código en capas concéntricas donde las dependencias solo apuntan hacia adentro. El núcleo (dominio) no conoce frameworks, BD ni UI.

```
┌─────────────────────────────────────┐
│          Frameworks & Drivers        │  Express, Prisma, React, Mailer
│  ┌───────────────────────────────┐  │
│  │     Interface Adapters         │  │  Controllers, Repositories, Presenters
│  │  ┌─────────────────────────┐  │  │
│  │  │    Application Layer     │  │  │  Use Cases / Services
│  │  │  ┌───────────────────┐  │  │  │
│  │  │  │   Domain / Entities│  │  │  │  Entidades, Value Objects, Reglas puras
│  │  │  └───────────────────┘  │  │  │
│  │  └─────────────────────────┘  │  │
│  └───────────────────────────────┘  │
└─────────────────────────────────────┘
          Dependencias → hacia adentro
```

```typescript
// Domain — no conoce Express, Prisma ni nada externo
export class User {
  constructor(
    public readonly id: string,
    public readonly email: string,
    private password: string,
  ) {}

  isAdmin(): boolean {
    return this.email.endsWith('@company.com');
  }
}

// Application (Use Case) — orquesta, depende de interfaces (no de implementaciones)
export class CreateUserUseCase {
  constructor(
    private userRepo: IUserRepository,   // interfaz, no Prisma
    private mailer: IMailService,        // interfaz, no Nodemailer
  ) {}

  async execute(dto: CreateUserDTO): Promise<User> {
    const existing = await this.userRepo.findByEmail(dto.email);
    if (existing) throw new ConflictError('Email ya en uso');

    const user = new User(uuid(), dto.email, await hash(dto.password));
    await this.userRepo.save(user);
    await this.mailer.sendWelcome(user.email);
    return user;
  }
}

// Interface Adapter — conecta el Use Case con Express
export class UserController {
  constructor(private createUser: CreateUserUseCase) {}

  async create(req: Request, res: Response) {
    const user = await this.createUser.execute(req.body);
    res.status(201).json({ id: user.id });
  }
}

// Frameworks layer — implementación concreta de las interfaces
export class PrismaUserRepository implements IUserRepository {
  async findByEmail(email: string) {
    return prisma.user.findUnique({ where: { email } });
  }
}
```

**Cuándo usarlo:** dominio complejo con muchas reglas de negocio, equipos grandes, necesidad de testear la lógica de negocio sin infraestructura.

**Cuándo no:** CRUDs simples, MVPs, proyectos pequeños — el overhead no se justifica.

---

## Arquitectura Hexagonal (Ports & Adapters)

Variante de Clean Architecture formulada por Alistair Cockburn. El núcleo de la aplicación expone "puertos" (interfaces) y el mundo exterior se conecta mediante "adaptadores".

```
          HTTP REST          CLI         Tests
              │               │            │
          [Adapter]       [Adapter]    [Adapter]
              │               │            │
     ┌────────┴───────────────┴────────────┴────────┐
     │                                               │
     │             Aplicación (Hexágono)             │
     │                                               │
     │    Puerto entrada    │    Puerto salida        │
     └──────────────────────┴────────────────────────┘
                                  │            │
                              [Adapter]    [Adapter]
                                  │            │
                              PostgreSQL    Redis
```

La diferencia con Clean Architecture es conceptual: el hexágono trata entradas (HTTP, CLI, eventos) y salidas (BD, email, cache) simétricamente como adaptadores intercambiables.

```typescript
// Puerto de entrada (driving port)
interface ICreateOrderUseCase {
  execute(dto: CreateOrderDTO): Promise<Order>;
}

// Puerto de salida (driven port)
interface IOrderRepository {
  save(order: Order): Promise<Order>;
}

// Adaptador de entrada — HTTP
class OrderController {
  constructor(private useCase: ICreateOrderUseCase) {}
  async create(req: Request, res: Response) { ... }
}

// Adaptador de salida — BD
class PostgresOrderRepository implements IOrderRepository {
  async save(order: Order) { ... }
}

// En tests: adaptador de salida en memoria
class InMemoryOrderRepository implements IOrderRepository {
  private orders: Order[] = [];
  async save(order: Order) {
    this.orders.push(order);
    return order;
  }
}
```

---

## Arquitectura Event-Driven

Los componentes se comunican emitiendo y escuchando eventos en lugar de llamarse directamente. Desacopla productores de consumidores.

```
Servicio A           Event Bus / Queue           Servicio B
   │                       │                         │
   │── emit('order.created', data) ──→               │
   │                       │── → UserNotificationService
   │                       │── → InventoryService
   │                       │── → AnalyticsService
```

```typescript
// Event Emitter nativo de Node
import EventEmitter from 'events';

const bus = new EventEmitter();

// Productor — no sabe quién escucha
async function createOrder(dto: CreateOrderDTO) {
  const order = await db.order.create({ data: dto });
  bus.emit('order.created', order); // dispara y olvida
  return order;
}

// Consumidores — independientes entre sí
bus.on('order.created', async (order) => {
  await sendConfirmationEmail(order.userEmail);
});

bus.on('order.created', async (order) => {
  await updateInventory(order.items);
});

bus.on('order.created', async (order) => {
  await trackAnalytics('purchase', order);
});
```

**Con queues (Bull, RabbitMQ, Kafka):** los eventos persisten y se procesan aunque el consumidor esté caído. Añade retries, dead-letter queues y procesamiento distribuido.

**Cuándo usarlo:** acciones con múltiples efectos secundarios, sistemas distribuidos, cuando los efectos secundarios no deben bloquear la operación principal.

**Cuándo no:** flujos donde necesitas la respuesta del consumidor de forma síncrona.

---

## Monolito vs Microservicios

### Monolito

Todo el código en un único proceso desplegable.

```
┌──────────────────────────────────┐
│            Monolito               │
│  Users │ Orders │ Products │ Auth │
│     (misma BD, mismo proceso)    │
└──────────────────────────────────┘
```

**Ventajas:** simple de desarrollar, desplegar y depurar. Transacciones ACID entre módulos. Sin latencia de red entre servicios. Ideal para equipos pequeños y productos en etapas tempranas.

**Desventajas:** escala toda la app aunque solo un módulo tenga carga. Un bug puede tirar todo. Difícil de adoptar tecnologías distintas por módulo.

### Microservicios

Cada dominio es un servicio independiente con su propia BD y proceso de despliegue.

```
API Gateway
    │
    ├── User Service     (Node + PostgreSQL)
    ├── Order Service    (Node + PostgreSQL)
    ├── Product Service  (Python + MongoDB)
    └── Notification Service (Go + Redis)
```

**Ventajas:** escala independiente por servicio, deploys independientes, equipos autónomos, tecnología por servicio.

**Desventajas:** complejidad operacional enorme, latencia de red, transacciones distribuidas difíciles, debugging complejo.

> [!WARNING]
> Microservicios son una solución a problemas de escala organizacional y operacional, no de complejidad de código. La mayoría de aplicaciones no los necesitan. Empieza con un monolito bien modularizado y extrae servicios cuando tengas una razón real para hacerlo.

---

## Cuándo usar cada arquitectura

| Escenario | Arquitectura recomendada |
|-----------|-------------------------|
| CRUD simple, MVP, proyecto pequeño | MVC o capas básicas |
| Dominio complejo con muchas reglas de negocio | Clean Architecture |
| Necesitas intercambiar implementaciones (BD, email) fácilmente | Hexagonal |
| Múltiples efectos secundarios desacoplados | Event-driven |
| Escala y equipo distribuido grande | Microservicios (con cuidado) |

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
