---
title: "Patrones de Diseño de Comportamiento — Observer, Strategy, Command, State"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - patrones-de-diseno
  - comportamiento
  - observer
  - strategy
  - command
  - state
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Patrones de Diseño - Creacionales]]"
  - "[[Patrones de Diseño - Estructurales]]"
  - "[[Reactividad]]"
  - "[[Signals]]"
  - "[[Principios SOLID]]"
---

# Patrones de Diseño de Comportamiento — Observer, Strategy, Command, State

Los patrones de comportamiento se ocupan de la comunicación y responsabilidades entre objetos. Definen protocolos claros para que los objetos interactúen sin acoplarse entre sí.

---

## Observer

Define una relación uno-a-muchos: cuando un objeto (el "sujeto") cambia de estado, notifica automáticamente a todos sus dependientes (los "observadores").

```typescript
// Interfaz del observador
interface Observer<T> {
  update(data: T): void;
}

// El sujeto — mantiene la lista de observadores
class EventEmitter<T> {
  private observers: Observer<T>[] = [];

  subscribe(observer: Observer<T>): () => void {
    this.observers.push(observer);
    // devuelve función de unsuscribe
    return () => {
      this.observers = this.observers.filter(o => o !== observer);
    };
  }

  protected notify(data: T) {
    this.observers.forEach(o => o.update(data));
  }
}

// Sujeto concreto
class CartStore extends EventEmitter<{ items: CartItem[]; total: number }> {
  private items: CartItem[] = [];

  addItem(item: CartItem) {
    this.items.push(item);
    const total = this.items.reduce((sum, i) => sum + i.price, 0);
    this.notify({ items: this.items, total }); // notifica a todos
  }
}

// Observadores concretos
const cart = new CartStore();

const unsubscribeUI = cart.subscribe({
  update({ items, total }) {
    renderCartUI(items, total);
  },
});

const unsubscribeAnalytics = cart.subscribe({
  update({ items }) {
    trackEvent('cart_updated', { itemCount: items.length });
  },
});

cart.addItem({ id: '1', name: 'Teclado', price: 89.99 });
// → renderCartUI se ejecuta
// → trackEvent se ejecuta

unsubscribeAnalytics(); // dejar de escuchar
```

**Observer en el stack:**
- **Angular Signals + `effect()`** — el signal es el sujeto; los effects son los observadores
- **RxJS Observables** — la implementación más completa del patrón en JS
- **React `useState`** — React actúa como observador del estado; re-renderiza cuando cambia
- **Node.js `EventEmitter`** — implementación nativa del patrón

---

## Strategy

Define una familia de algoritmos, los encapsula en objetos separados e intercambiables. Permite cambiar el algoritmo usado en tiempo de ejecución.

```typescript
// Interfaz de la estrategia
interface SortStrategy<T> {
  sort(data: T[]): T[];
}

// Estrategias concretas
class BubbleSort<T> implements SortStrategy<T> {
  sort(data: T[]): T[] {
    const arr = [...data];
    for (let i = 0; i < arr.length; i++) {
      for (let j = 0; j < arr.length - i - 1; j++) {
        if (arr[j] > arr[j + 1]) [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
      }
    }
    return arr;
  }
}

class QuickSort<T> implements SortStrategy<T> {
  sort(data: T[]): T[] {
    if (data.length <= 1) return data;
    const pivot = data[Math.floor(data.length / 2)];
    const left  = data.filter(x => x < pivot);
    const right = data.filter(x => x > pivot);
    return [...this.sort(left), pivot, ...this.sort(right)];
  }
}

// Contexto — usa la estrategia sin saber cuál es
class DataSorter<T> {
  constructor(private strategy: SortStrategy<T>) {}

  setStrategy(strategy: SortStrategy<T>) {
    this.strategy = strategy;
  }

  sort(data: T[]): T[] {
    return this.strategy.sort(data);
  }
}

const sorter = new DataSorter(new QuickSort<number>());
sorter.sort([5, 3, 1, 4, 2]); // quicksort

// Cambiar estrategia en runtime
if (data.length < 10) sorter.setStrategy(new BubbleSort());
```

**Strategy como funciones (forma idiomática en JS):**

```typescript
// En JS/TS moderno, las estrategias son simplemente funciones
type PricingStrategy = (basePrice: number, user: User) => number;

const regularPricing:  PricingStrategy = (price) => price;
const premiumPricing:  PricingStrategy = (price) => price * 0.8;
const vipPricing:      PricingStrategy = (price) => price * 0.65;

const strategies: Record<string, PricingStrategy> = {
  regular: regularPricing,
  premium: premiumPricing,
  vip:     vipPricing,
};

function calculatePrice(basePrice: number, user: User): number {
  const strategy = strategies[user.tier] ?? regularPricing;
  return strategy(basePrice, user);
}
```

**En Express:** los middlewares son estrategias intercambiables para manejar autenticación, validación o logging.

---

## Command

Encapsula una petición como un objeto, permitiendo parametrizar clientes con operaciones, encolar peticiones, registrar logs y soportar operaciones deshechas (undo).

```typescript
// Interfaz del comando
interface Command {
  execute(): Promise<void>;
  undo(): Promise<void>;
}

// Comandos concretos
class CreateUserCommand implements Command {
  private createdUserId: string | null = null;

  constructor(private data: CreateUserDTO) {}

  async execute() {
    const user = await db.user.create({ data: this.data });
    this.createdUserId = user.id; // guardar para undo
  }

  async undo() {
    if (this.createdUserId) {
      await db.user.delete({ where: { id: this.createdUserId } });
    }
  }
}

class SendEmailCommand implements Command {
  constructor(private to: string, private template: string) {}

  async execute() {
    await mailer.send({ to: this.to, template: this.template });
  }

  async undo() {
    // Los emails no se pueden "deshacer", pero puedes enviar uno de corrección
    await mailer.send({ to: this.to, template: 'correction' });
  }
}

// Invoker — ejecuta y mantiene historial
class CommandQueue {
  private history: Command[] = [];

  async execute(command: Command) {
    await command.execute();
    this.history.push(command);
  }

  async undoLast() {
    const last = this.history.pop();
    if (last) await last.undo();
  }

  async undoAll() {
    while (this.history.length > 0) {
      await this.undoLast();
    }
  }
}

// Uso — fácil de extender con nuevos comandos sin cambiar el Invoker
const queue = new CommandQueue();
await queue.execute(new CreateUserCommand({ email: 'test@example.com', ... }));
await queue.execute(new SendEmailCommand('test@example.com', 'welcome'));
await queue.undoLast(); // deshacer el email
```

**Command en el stack:**
- **Server Actions de Next.js** — cada action es un Command encapsulado
- **Redux actions** — el patrón Redux es Command + Event Sourcing
- **Git commits** — cada commit es un Command que puede revertirse con `git revert`

---

## State

Permite que un objeto cambie su comportamiento cuando su estado interno cambia. El objeto parece cambiar de clase.

```typescript
// Estados posibles de un pedido
interface OrderState {
  name: string;
  confirm(order: Order): void;
  ship(order: Order): void;
  cancel(order: Order): void;
}

class PendingState implements OrderState {
  name = 'pending';

  confirm(order: Order) {
    console.log('Pedido confirmado');
    order.setState(new ConfirmedState());
  }

  ship(order: Order) {
    throw new Error('No puedes enviar un pedido pendiente');
  }

  cancel(order: Order) {
    console.log('Pedido cancelado');
    order.setState(new CancelledState());
  }
}

class ConfirmedState implements OrderState {
  name = 'confirmed';

  confirm(order: Order) {
    throw new Error('El pedido ya está confirmado');
  }

  ship(order: Order) {
    console.log('Pedido enviado');
    order.setState(new ShippedState());
  }

  cancel(order: Order) {
    console.log('Pedido cancelado desde confirmado');
    order.setState(new CancelledState());
  }
}

class ShippedState implements OrderState {
  name = 'shipped';
  confirm(order: Order) { throw new Error('Ya enviado'); }
  ship(order: Order)    { throw new Error('Ya enviado'); }
  cancel(order: Order)  { throw new Error('No se puede cancelar un pedido enviado'); }
}

class CancelledState implements OrderState {
  name = 'cancelled';
  confirm(order: Order) { throw new Error('Pedido cancelado'); }
  ship(order: Order)    { throw new Error('Pedido cancelado'); }
  cancel(order: Order)  { throw new Error('Ya cancelado'); }
}

// Contexto
class Order {
  private state: OrderState = new PendingState();

  setState(state: OrderState) {
    console.log(`Estado: ${this.state.name} → ${state.name}`);
    this.state = state;
  }

  getState() { return this.state.name; }
  confirm()   { this.state.confirm(this); }
  ship()      { this.state.ship(this); }
  cancel()    { this.state.cancel(this); }
}

const order = new Order();
order.confirm(); // pending → confirmed
order.ship();    // confirmed → shipped
order.cancel();  // Error: No se puede cancelar un pedido enviado
```

---

## Chain of Responsibility

Pasa una petición por una cadena de manejadores. Cada manejador decide si la procesa o la pasa al siguiente.

```typescript
abstract class Handler {
  protected next: Handler | null = null;

  setNext(handler: Handler): Handler {
    this.next = handler;
    return handler; // permite encadenar: h1.setNext(h2).setNext(h3)
  }

  abstract handle(request: Request): Response | null;
}

class AuthHandler extends Handler {
  handle(req: Request): Response | null {
    if (!req.headers.authorization) {
      return { status: 401, body: { error: 'Unauthorized' } };
    }
    return this.next?.handle(req) ?? null;
  }
}

class RateLimitHandler extends Handler {
  handle(req: Request): Response | null {
    if (isRateLimited(req.ip)) {
      return { status: 429, body: { error: 'Too Many Requests' } };
    }
    return this.next?.handle(req) ?? null;
  }
}

class BusinessHandler extends Handler {
  handle(req: Request): Response | null {
    return { status: 200, body: processRequest(req) };
  }
}

// Construir la cadena
const auth = new AuthHandler();
auth.setNext(new RateLimitHandler()).setNext(new BusinessHandler());

const response = auth.handle(incomingRequest);
```

**El sistema de middlewares de Express es Chain of Responsibility.** Cada `app.use()` agrega un eslabón a la cadena; `next()` pasa al siguiente.

---

## Template Method

Define el esqueleto de un algoritmo en una clase base, dejando algunos pasos para que las subclases los implementen.

```typescript
// Clase base — define el "template" del algoritmo
abstract class DataExporter {
  // Template Method — el algoritmo completo
  async export(data: unknown[]): Promise<Buffer> {
    const validated = await this.validate(data);   // paso 1
    const formatted = this.format(validated);       // paso 2 — abstracto
    const header = this.buildHeader();              // paso 3 — abstracto
    return this.compile(header, formatted);         // paso 4
  }

  // Paso con implementación por defecto
  protected async validate(data: unknown[]): Promise<unknown[]> {
    return data.filter(Boolean);
  }

  // Pasos abstractos — cada subclase los implementa
  protected abstract format(data: unknown[]): string[][];
  protected abstract buildHeader(): string[];
  protected abstract compile(header: string[], rows: string[][]): Buffer;
}

class CSVExporter extends DataExporter {
  protected format(data: unknown[]) {
    return data.map(item => Object.values(item as object).map(String));
  }

  protected buildHeader() {
    return ['ID', 'Nombre', 'Email'];
  }

  protected compile(header: string[], rows: string[][]) {
    const csv = [header, ...rows].map(r => r.join(',')).join('\n');
    return Buffer.from(csv, 'utf-8');
  }
}
```

---

## Cuándo usar cada uno

| Patrón | Usar cuando... |
|--------|----------------|
| **Observer** | Un cambio en un objeto debe notificar a múltiples dependientes sin conocerlos |
| **Strategy** | Necesitas intercambiar algoritmos o comportamientos en runtime |
| **Command** | Quieres encapsular operaciones para encolarlas, registrarlas o deshacerlas |
| **State** | El comportamiento de un objeto cambia drásticamente según su estado interno |
| **Chain of Responsibility** | Múltiples handlers pueden procesar una petición; el orden importa |
| **Template Method** | El algoritmo tiene un esqueleto fijo pero algunos pasos varían por implementación |

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
