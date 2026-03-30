---
title: "Reactividad — Cómo los UIs responden al cambio de estado"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - reactividad
  - estado
  - frontend
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[useState]]"
  - "[[Signals]]"
  - "[[App Router]]"
---

# Reactividad — Cómo los UIs responden al cambio de estado

Reactividad es la capacidad de un sistema de propagar cambios automáticamente: cuando un dato cambia, todo lo que depende de él se actualiza sin que tengas que indicarlo explícitamente. Es el principio detrás de React, Angular Signals, Vue y prácticamente cualquier framework UI moderno.

---

## El problema que resuelve

Sin reactividad, cada vez que cambia un dato tienes que actualizar el DOM manualmente:

```js
// Sin reactividad — imperativo, frágil, no escala
let count = 0;

function increment() {
  count++;
  document.getElementById('counter').textContent = count; // manual
  document.getElementById('title').textContent = `Total: ${count}`; // manual
  // ¿y si olvidaste un elemento? bug silencioso
}
```

Con reactividad, declaras las dependencias una vez y el sistema se encarga de propagar:

```tsx
// Con reactividad — declarativo, automático
const [count, setCount] = useState(0);

// React sabe que estos elementos dependen de `count`
// y los actualiza solos cuando cambia
return (
  <>
    <span>{count}</span>
    <h1>Total: {count}</h1>
  </>
);
```

---

## Modelos de reactividad: granular vs global

### Reactividad global (React / virtual DOM)

React no rastrea qué datos específicos usa cada componente. Cuando el estado cambia, re-renderiza el componente completo y usa el Virtual DOM para calcular qué cambió en el DOM real.

```
Estado cambia → Re-render del componente → Diff virtual DOM → Actualizar DOM real
```

**Ventajas:** simple de razonar, no hay "suscripciones" explícitas.
**Desventajas:** puede re-renderizar más de lo necesario; requiere optimizaciones manuales (`memo`, `useMemo`, `useCallback`) en componentes grandes.

### Reactividad granular (Signals / Vue / SolidJS)

El sistema rastrea exactamente qué partes del UI leen cada dato. Solo se actualiza lo que realmente depende del valor que cambió.

```
Signal cambia → Solo los efectos/computed que lo leen se actualizan
```

```typescript
// Angular Signals — reactividad granular
const price = signal(100);
const tax = signal(0.21);
const total = computed(() => price() * (1 + tax())); // depende de price y tax

price.set(200); // solo `total` y los elementos que lo leen se actualizan
```

**Ventajas:** más eficiente, sin re-renders innecesarios.
**Desventajas:** más verboso, debes gestionar explícitamente las señales.

---

## El ciclo reactivo

En cualquier sistema reactivo, hay tres actores:

```
┌─────────┐    dispara    ┌──────────┐    recalcula   ┌─────────────┐
│  Estado │ ──────────→  │ Computed │ ─────────────→ │     UI      │
│ (signal)│               │ (derived)│                │  (effects)  │
└─────────┘               └──────────┘                └─────────────┘
     ↑                                                       │
     └─────────────────── interacción ─────────────────────┘
```

| Actor | React | Angular Signals | RxJS |
|-------|-------|----------------|------|
| Estado | `useState` | `signal()` | `BehaviorSubject` |
| Derivado | `useMemo` | `computed()` | `.pipe(map(...))` |
| Efectos secundarios | `useEffect` | `effect()` | `.subscribe()` |

---

## Reactividad en el servidor: Next.js

Next.js extiende la reactividad más allá del cliente. Con el App Router, el servidor también puede "reaccionar" a cambios de datos y actualizar el HTML generado:

```tsx
// Revalidación bajo demanda — el servidor reacciona al cambio
import { revalidateTag } from 'next/cache';

// Cuando un artículo se actualiza en Strapi:
await revalidateTag('articles'); // Next.js regenera las páginas que usaron ese tag
```

---

## Cuándo importa elegir bien el modelo

| Escenario | Modelo recomendado |
|-----------|-------------------|
| Componentes simples con poco estado | Reactividad global (React vanilla) |
| Listas largas con actualizaciones frecuentes | Reactividad granular (Signals, SolidJS) |
| Estado de servidor con SSR | Reactividad de servidor (Next.js ISR) |
| Streams de datos en tiempo real | Reactividad con Observables (RxJS) |
| Formularios complejos | Reactividad local + validación reactiva |

---

## Errores comunes

> [!WARNING]
> **Mutar estado directamente:** Los sistemas reactivos solo detectan cambios cuando reemplazas el valor, no cuando lo mutas. `array.push(item)` no dispara un re-render en React; `setArray([...array, item])` sí.
>
> **Efectos circulares:** Un `effect()` que modifica un signal del que depende crea un ciclo infinito. Los efectos son para sincronizar con el mundo exterior (DOM, localStorage, fetch), no para transformar estado.
>
> **Over-reactivity:** No todo necesita ser estado reactivo. Las constantes, los valores que se calculan una vez, o los datos que no afectan al UI no deberían ser signals ni useState.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
