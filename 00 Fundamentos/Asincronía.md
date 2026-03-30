---
title: "Asincronía en JavaScript — Promises, async/await y patrones"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - asincronia
  - javascript
  - promises
  - async-await
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Event Loop]]"
  - "[[Middlewares]]"
  - "[[App Router]]"
---

# Asincronía en JavaScript — Promises, async/await y patrones

JavaScript es single-threaded pero no bloqueante. La asincronía permite que operaciones lentas (I/O, red, timers) se procesen sin congelar el hilo principal. Entender el modelo mental correcto evita la mayoría de los bugs difíciles de depurar.

---

## El problema de la asincronía

Algunas operaciones toman tiempo indeterminado: leer un archivo, hacer una petición HTTP, acceder a una base de datos. Si esperáramos bloqueando el hilo, la app dejaría de responder.

```js
// ❌ Imaginario — no existe en JS, pero así pensamos erróneamente
const data = fetch('/api/users'); // "espera aquí"
console.log(data); // usa el resultado inmediatamente
```

La solución real: decirle a JS "cuando termines esto, ejecuta esta función":

```js
// ✅ Modelo real — callback, Promise, o async/await
fetch('/api/users')
  .then(res => res.json())
  .then(data => console.log(data));
```

---

## Evolución: Callbacks → Promises → async/await

### 1. Callbacks (el origen)

```js
fs.readFile('file.txt', 'utf-8', function(err, data) {
  if (err) {
    console.error(err);
    return;
  }
  console.log(data);
});
```

**Problema:** anidar callbacks crea el "Callback Hell":

```js
// Difícil de leer, mantener y manejar errores
getUser(id, function(err, user) {
  if (err) return handleError(err);
  getPosts(user.id, function(err, posts) {
    if (err) return handleError(err);
    getComments(posts[0].id, function(err, comments) {
      if (err) return handleError(err);
      // ya vamos 3 niveles adentro...
    });
  });
});
```

### 2. Promises

Una Promise representa un valor que estará disponible en el futuro. Tiene tres estados: `pending`, `fulfilled`, `rejected`.

```js
// Crear una Promise
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    if (Math.random() > 0.5) {
      resolve('éxito');
    } else {
      reject(new Error('falló'));
    }
  }, 1000);
});

// Consumir con .then / .catch / .finally
promise
  .then(value => console.log('Resultado:', value))
  .catch(err => console.error('Error:', err.message))
  .finally(() => console.log('Siempre se ejecuta'));
```

**Encadenamiento — resuelve el Callback Hell:**

```js
getUser(id)
  .then(user => getPosts(user.id))      // cada .then recibe el resultado del anterior
  .then(posts => getComments(posts[0].id))
  .then(comments => render(comments))
  .catch(err => handleError(err));       // un solo catch para toda la cadena
```

### 3. async/await (sugar syntax sobre Promises)

`async/await` hace que el código asíncrono se lea como si fuera síncrono. Internamente sigue siendo Promises.

```js
async function loadData() {
  try {
    const user = await getUser(id);        // "espera" el resultado
    const posts = await getPosts(user.id);
    const comments = await getComments(posts[0].id);
    render(comments);
  } catch (err) {
    handleError(err);
  }
}
```

---

## Patrones de concurrencia

### Secuencial vs paralelo — el error más costoso

```js
// ❌ Secuencial — espera cada uno antes de empezar el siguiente
// Total: 300ms (100 + 100 + 100)
const users = await fetchUsers();     // 100ms
const posts = await fetchPosts();     // 100ms
const settings = await fetchSettings(); // 100ms

// ✅ Paralelo con Promise.all — arrancan los tres al mismo tiempo
// Total: ~100ms (el más lento de los tres)
const [users, posts, settings] = await Promise.all([
  fetchUsers(),
  fetchPosts(),
  fetchSettings(),
]);
```

> [!IMPORTANT]
> `Promise.all` falla si **cualquiera** de las promises falla. Si necesitas que fallen de forma independiente, usa `Promise.allSettled`.

### Promise.allSettled — resultados individuales sin fallo global

```js
const results = await Promise.allSettled([
  fetchUsers(),
  fetchPosts(),
  fetchSettings(),
]);

results.forEach(result => {
  if (result.status === 'fulfilled') {
    console.log('OK:', result.value);
  } else {
    console.error('Falló:', result.reason);
  }
});
```

### Promise.race — el primero en responder gana

```js
// Timeout pattern — si la petición tarda más de 5s, rechaza
const withTimeout = (promise, ms) => Promise.race([
  promise,
  new Promise((_, reject) =>
    setTimeout(() => reject(new Error('Timeout')), ms)
  ),
]);

const data = await withTimeout(fetchData(), 5000);
```

---

## Manejo de errores

```js
// En async functions — try/catch es idiomático
async function createUser(data) {
  try {
    const user = await db.user.create(data);
    return { success: true, user };
  } catch (err) {
    if (err.code === 'P2002') { // Prisma unique constraint
      return { success: false, error: 'El email ya está en uso' };
    }
    throw err; // re-lanzar errores inesperados
  }
}

// Wrapper para evitar try/catch repetitivo
async function safeAwait(promise) {
  try {
    const data = await promise;
    return [null, data];
  } catch (err) {
    return [err, null];
  }
}

const [err, user] = await safeAwait(db.user.findById(id));
if (err) return res.status(404).json({ error: 'Not found' });
```

---

## Errores comunes

> [!WARNING]
> **await en loops:** `for...of` con `await` es secuencial. Si puedes paralelizar, usa `Promise.all(array.map(...))`.
>
> ```js
> // ❌ Secuencial — N peticiones una tras otra
> for (const id of ids) {
>   await fetchUser(id);
> }
>
> // ✅ Paralelo — N peticiones simultáneas
> await Promise.all(ids.map(id => fetchUser(id)));
> ```
>
> **Olvidar await:** Si llamas una función async sin `await`, obtienes una Promise, no el valor. El código no falla inmediatamente — el bug aparece después.
>
> **async en callbacks de array:** `.forEach` no espera Promises. Usa `for...of` o `Promise.all` con `.map`.
>
> ```js
> // ❌ forEach no espera — todos los awaits se ignoran
> items.forEach(async (item) => {
>   await processItem(item);
> });
>
> // ✅ for...of espera correctamente
> for (const item of items) {
>   await processItem(item);
> }
> ```

---

## Relación con el Event Loop

La asincronía en JS funciona gracias al [[Event Loop]]. Cada `await` devuelve el control al Event Loop, que puede procesar otros eventos mientras espera. Ver esa nota para entender el orden de ejecución entre microtareas y macrotareas.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
