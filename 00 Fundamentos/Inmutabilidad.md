---
title: "Inmutabilidad — Por qué no mutar datos directamente"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - inmutabilidad
  - estado
  - funcional
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[useState]]"
  - "[[Reactividad]]"
  - "[[Signals]]"
---

# Inmutabilidad — Por qué no mutar datos directamente

Un valor es inmutable cuando no puede cambiarse después de crearse. En lugar de modificar el dato existente, se crea uno nuevo con los cambios aplicados. Es el principio subyacente detrás de por qué no debes hacer `state.push(item)` en React o `array.splice()` con Angular Signals.

---

## El problema de la mutación

JavaScript pasa objetos y arrays por referencia. Cuando mutes un objeto, todos los que tienen una referencia a él ven el cambio, aunque no lo esperaran:

```js
const user = { name: 'Daniel', role: 'admin' };
const copy = user; // no es una copia — es la misma referencia

copy.role = 'viewer';
console.log(user.role); // 'viewer' — ¡mutaste el original!
```

Esto hace que el comportamiento del sistema sea difícil de predecir y rastrear.

---

## Por qué los sistemas reactivos necesitan inmutabilidad

React, Angular Signals y la mayoría de los sistemas reactivos detectan cambios comparando referencias:

```js
// React internamente hace algo así
if (prevState === nextState) return; // sin cambio — no re-renderizar

// Si mutas el objeto, la referencia es la misma → React no detecta el cambio
state.count = 5;         // ❌ misma referencia
setState({ ...state, count: 5 }); // ✅ nueva referencia → React detecta el cambio
```

```typescript
// Angular Signals — mismo principio
const items = signal([1, 2, 3]);

items().push(4);           // ❌ muta el array interno — Signal no dispara
items.set([...items(), 4]); // ✅ nuevo array — Signal detecta el cambio
```

---

## Patrones de actualización inmutable

### Objetos

```js
const user = { name: 'Daniel', role: 'admin', age: 30 };

// Actualizar una propiedad — spread operator
const updatedUser = { ...user, role: 'viewer' };

// Actualizar propiedad anidada — spread en cada nivel
const config = {
  theme: 'dark',
  notifications: { email: true, push: false },
};

const updated = {
  ...config,
  notifications: { ...config.notifications, push: true }, // spread anidado
};
```

### Arrays

```js
const items = [1, 2, 3, 4, 5];

// Agregar al final
const withNew = [...items, 6];

// Agregar al principio
const withFirst = [0, ...items];

// Eliminar por índice
const without = items.filter((_, i) => i !== 2);

// Eliminar por valor
const withoutValue = items.filter(item => item !== 3);

// Actualizar un elemento
const updated = items.map((item, i) => i === 2 ? 99 : item);

// Insertar en posición
const inserted = [...items.slice(0, 2), 99, ...items.slice(2)];
```

### Arrays de objetos — el caso más común en apps

```js
const users = [
  { id: 1, name: 'Daniel', active: true },
  { id: 2, name: 'Carlos', active: false },
];

// Actualizar un usuario específico
const updatedUsers = users.map(user =>
  user.id === 1 ? { ...user, active: false } : user
);

// Eliminar un usuario
const withoutUser = users.filter(user => user.id !== 2);

// En React con useState:
setUsers(prev => prev.map(u => u.id === id ? { ...u, ...changes } : u));
```

---

## Immutabilidad profunda con librerías

Cuando la estructura anidada es muy profunda, el spread manual se vuelve verboso. `immer` resuelve esto dejando escribir código "mutante" que internamente produce un nuevo objeto:

```js
import produce from 'immer';

const state = {
  users: [{ id: 1, address: { city: 'Madrid', zip: '28001' } }],
};

// immer intercepta las mutaciones y produce un nuevo objeto inmutable
const nextState = produce(state, draft => {
  draft.users[0].address.city = 'Barcelona'; // parece mutación, no lo es
});

console.log(state.users[0].address.city);      // 'Madrid' — sin cambios
console.log(nextState.users[0].address.city);  // 'Barcelona' — nuevo objeto
```

---

## Cuándo aplicar y cuándo no

**Aplicar inmutabilidad:**
- Estado de UI (React, Angular, Vue)
- Datos que se comparten entre múltiples consumidores
- Cuando necesitas historial o "tiempo atrás" (undo/redo)
- En transformaciones funcionales de datos (`.map`, `.filter`, `.reduce`)

**No necesitas inmutabilidad estricta:**
- Variables locales temporales dentro de una función
- Datos que no se comparten ni son estado reactivo
- Estructuras de datos de alto rendimiento donde la copia es demasiado costosa (buffers, estructuras de datos especializadas)

---

## La inmutabilidad y el principio funcional

La inmutabilidad es una de las ideas centrales de la programación funcional: las funciones no modifican sus inputs, solo producen outputs nuevos. Esto hace que las funciones sean predecibles y fáciles de testear:

```js
// Función pura — inmutable, predecible, testeable
function addItem(cart, item) {
  return { ...cart, items: [...cart.items, item] };
}

// Función impura — muta el input, impredecible
function addItem(cart, item) {
  cart.items.push(item); // ❌ modifica el parámetro
  return cart;
}
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
