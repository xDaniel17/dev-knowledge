---
title: "Event Loop de Node.js — Cómo funciona la concurrencia"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nodejs
  - event-loop
  - concurrencia
  - async
category: "01 Stack"
subcategory: "Node"
author: Daniel
version: 1.0.0
status: active
related: []
---

# Event Loop de Node.js — Cómo funciona la concurrencia

Node.js es single-threaded pero maneja miles de operaciones concurrentes gracias al Event Loop. Entender cómo funciona explica por qué el código async se comporta como lo hace y cómo evitar bloquear el hilo principal.

---

## El modelo básico

Node.js tiene un único hilo de JavaScript. Las operaciones de I/O (archivos, red, timers) se delegan a libuv y al sistema operativo. Cuando terminan, sus callbacks se encolan para que el Event Loop los procese.

```
   ┌───────────────────────────────────────────────────┐
   │                  Tu código JS                      │
   │              (Call Stack — LIFO)                   │
   └───────────────────┬───────────────────────────────┘
                       │ delega I/O
   ┌───────────────────▼───────────────────────────────┐
   │              libuv / OS                            │
   │     (Thread Pool para fs, crypto, dns)             │
   │     (Kernel async para TCP, HTTP, pipes)           │
   └───────────────────┬───────────────────────────────┘
                       │ callbacks listos
   ┌───────────────────▼───────────────────────────────┐
   │                 Event Loop                         │
   │  timers → pending → idle → poll → check → close   │
   └───────────────────────────────────────────────────┘
```

---

## Las fases del Event Loop

El Event Loop recorre estas fases en orden en cada "tick":

```
   ┌─────────────┐
   │   timers    │  ← setTimeout, setInterval callbacks
   └──────┬──────┘
          │
   ┌──────▼──────┐
   │  pending    │  ← callbacks de I/O del tick anterior
   └──────┬──────┘
          │
   ┌──────▼──────┐
   │    idle     │  ← uso interno de Node
   └──────┬──────┘
          │
   ┌──────▼──────┐
   │    poll     │  ← nuevos eventos de I/O (fs, net, etc.)
   └──────┬──────┘
          │
   ┌──────▼──────┐
   │    check    │  ← setImmediate callbacks
   └──────┬──────┘
          │
   ┌──────▼──────┐
   │    close    │  ← eventos 'close' (socket.destroy(), etc.)
   └─────────────┘
```

---

## Microtask Queue — máxima prioridad

Las microtareas (Promises, queueMicrotask) se procesan **entre cada fase** del Event Loop, antes de pasar a la siguiente. También `process.nextTick` se ejecuta aquí, antes que las Promises:

```
Orden de prioridad (de mayor a menor):
1. process.nextTick queue
2. Promise callbacks (.then, .catch, async/await)
3. setTimeout / setInterval (fase timers)
4. setImmediate (fase check)
5. I/O callbacks (fs.readFile, etc.)
```

---

## Ejemplo de orden de ejecución

```javascript
console.log('1 — sincrónico');

setTimeout(() => console.log('2 — setTimeout 0ms'), 0);

Promise.resolve().then(() => console.log('3 — Promise'));

process.nextTick(() => console.log('4 — nextTick'));

setImmediate(() => console.log('5 — setImmediate'));

console.log('6 — sincrónico');

// Output:
// 1 — sincrónico
// 6 — sincrónico
// 4 — nextTick       ← microtask, máxima prioridad
// 3 — Promise        ← microtask, después de nextTick
// 2 — setTimeout 0ms ← fase timers
// 5 — setImmediate   ← fase check
```

---

## Por qué no bloquear el hilo principal

Node procesa un callback a la vez. Si un callback toma mucho tiempo, bloquea todos los demás:

```javascript
// ❌ BLOQUEA el Event Loop — ninguna request puede procesarse mientras tanto
app.get('/report', (req, res) => {
  const result = heavyComputation(1_000_000); // cálculo síncrono costoso
  res.json(result);
});

// ✅ Delegar a Worker Thread o limitar el trabajo sincrónico
const { Worker } = require('worker_threads');

app.get('/report', (req, res) => {
  const worker = new Worker('./heavyTask.js');
  worker.on('message', result => res.json(result));
  worker.postMessage({ iterations: 1_000_000 });
});
```

Las operaciones de I/O (leer archivos, hacer peticiones HTTP) son seguras porque Node las delega al sistema operativo. El problema solo ocurre con cómputo intensivo en JavaScript puro.

---

## setTimeout vs Promise vs process.nextTick

```javascript
async function example() {
  // process.nextTick: antes de cualquier I/O, antes de Promises
  process.nextTick(() => console.log('nextTick'));

  // Promise: microtask, después de nextTick
  await Promise.resolve();
  console.log('después de await');

  // setTimeout: macrotask, en la fase timers
  setTimeout(() => console.log('setTimeout'), 0);

  // setImmediate: en la fase check, después de I/O
  setImmediate(() => console.log('setImmediate'));
}
```

**Cuándo usar cada uno:**
- `process.nextTick`: cuando necesitas ejecutar algo antes de que el Event Loop continúe (p.ej., emitir un error antes de que el listener esté registrado)
- `Promise` / `async-await`: para la mayoría de operaciones asíncronas
- `setImmediate`: cuando quieres ejecutar después de los eventos de I/O del ciclo actual
- `setTimeout(fn, 0)`: cuando quieres ejecutar en el próximo ciclo del timer, pero `setImmediate` es más predecible para este caso

---

## CPU-bound vs I/O-bound

```javascript
// I/O-bound — Node lo maneja bien solo
// El hilo JS no hace nada mientras espera la respuesta
const data = await fs.readFile('large-file.json', 'utf-8');
const response = await fetch('https://api.example.com/data');

// CPU-bound — problema potencial
// Este código ocupa el Call Stack completamente
const hash = crypto.createHash('sha256').update(largeBuffer).digest('hex');
// Para archivos muy grandes, usar crypto.createHash de forma streaming
// o delegar a worker_threads
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
