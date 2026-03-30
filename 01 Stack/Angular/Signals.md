---
title: "Angular Signals — Reactividad granular (v17+)"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - angular
  - signals
  - reactividad
  - typescript
category: "01 Stack"
subcategory: "Angular"
author: Daniel
version: 1.0.0
status: active
related: []
---

# Angular Signals — Reactividad granular (v17+)

Angular Signals es el nuevo sistema de reactividad de Angular, introducido como estable en v17. Permite detectar cambios de estado de forma granular sin necesidad de Zone.js ni de la estrategia `OnPush` manual.

---

## Por qué se introdujeron los Signals

El sistema de change detection de Angular históricamente dependía de Zone.js, que parchea las APIs del browser (setTimeout, fetch, eventos DOM) para disparar una verificación global del árbol de componentes. Esto era potente pero costoso: cualquier evento podía triggear checks en toda la app.

Los Signals resuelven esto registrando dependencias reactivas de forma precisa. Solo los componentes que leen un signal específico se re-renderizan cuando ese signal cambia.

---

## Las tres primitivas

### signal() — estado mutable

```typescript
import { signal } from '@angular/core';

// Crear un signal con valor inicial
const count = signal(0);

// Leer el valor (llamándolo como función)
console.log(count()); // 0

// Actualizar — reemplaza el valor
count.set(5);

// Actualizar basado en el valor anterior
count.update(prev => prev + 1);

// Para objetos: mutar sin reemplazar la referencia
const user = signal({ name: 'Daniel', role: 'admin' });
user.mutate(u => { u.name = 'Carlos'; });
```

### computed() — derivaciones reactivas

`computed` crea un signal de solo lectura que se recalcula automáticamente cuando sus dependencias cambian:

```typescript
import { signal, computed } from '@angular/core';

const price = signal(100);
const quantity = signal(3);
const taxRate = signal(0.21);

// Se recalcula solo cuando price, quantity o taxRate cambian
const total = computed(() => {
  const subtotal = price() * quantity();
  return subtotal + subtotal * taxRate();
});

console.log(total()); // 363
price.set(200);
console.log(total()); // 726 — actualizó automáticamente
```

### effect() — efectos secundarios reactivos

`effect` ejecuta una función cuando cambian los signals que lee. Equivale a un `useEffect` pero sin lista de dependencias manual:

```typescript
import { signal, effect } from '@angular/core';

const theme = signal<'light' | 'dark'>('light');

// Se ejecuta en la inicialización y cada vez que `theme` cambia
const cleanup = effect(() => {
  document.body.classList.toggle('dark', theme() === 'dark');
  console.log(`Tema cambiado a: ${theme()}`);
});
```

> [!WARNING]
> `effect` no debe modificar otros signals — puede causar ciclos infinitos. Si necesitas transformar estado, usa `computed`.

---

## Signals en componentes

```typescript
import { Component, signal, computed } from '@angular/core';

@Component({
  selector: 'app-counter',
  standalone: true,
  template: `
    <p>Contador: {{ count() }}</p>
    <p>Es par: {{ isEven() }}</p>
    <button (click)="increment()">+1</button>
    <button (click)="reset()">Reset</button>
  `,
})
export class CounterComponent {
  count = signal(0);
  isEven = computed(() => this.count() % 2 === 0);

  increment() {
    this.count.update(c => c + 1);
  }

  reset() {
    this.count.set(0);
  }
}
```

---

## Comparación con alternativas

| Característica | BehaviorSubject (RxJS) | useState (React) | Signal (Angular) |
|---|---|---|---|
| Lectura | `.value` o `.subscribe()` | variable del closure | llamada como función `count()` |
| Escritura | `.next(value)` | `setState(value)` | `.set(value)` |
| Derivación | `.pipe(map(...))` | useMemo | `computed()` |
| Efectos | `.subscribe()` | `useEffect` | `effect()` |
| Unsubscribe | Manual necesario | Automático | Automático |
| Async nativo | Sí (Observables) | No | No (usar con RxJS) |

---

## Migración progresiva

Los Signals coexisten con el sistema RxJS existente. Puedes convertir entre ambos con las funciones utilitarias:

```typescript
import { toSignal, toObservable } from '@angular/core/rxjs-interop';
import { interval } from 'rxjs';

// Observable → Signal
const timer$ = interval(1000);
const timerSignal = toSignal(timer$, { initialValue: 0 });

// Signal → Observable
const count = signal(0);
const count$ = toObservable(count);
count$.subscribe(v => console.log('Nuevo valor:', v));
```

Esto permite adoptar Signals gradualmente en proyectos existentes sin reescribir toda la lógica RxJS.

---

## Inputs como Signals (Angular 17.1+)

```typescript
import { Component, input } from '@angular/core';

@Component({
  selector: 'app-user-card',
  standalone: true,
  template: `<p>{{ user().name }}</p>`,
})
export class UserCardComponent {
  // Signal-based input — reemplaza @Input()
  user = input.required<{ name: string; role: string }>();

  // Input con valor por defecto
  showAvatar = input(true);
}
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
