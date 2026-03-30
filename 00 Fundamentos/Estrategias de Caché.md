---
title: "Estrategias de Caché — Velocidad sin datos obsoletos"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - cache
  - performance
  - backend
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[App Router]]"
  - "[[Estructura de proyecto]]"
  - "[[Event Loop]]"
---

# Estrategias de Caché — Velocidad sin datos obsoletos

El caché almacena resultados de operaciones costosas para servirlos más rápido en futuras peticiones. El reto no es guardar en caché — es saber cuándo invalidarlo. Como dijo Phil Karlton: *"Solo hay dos cosas difíciles en informática: invalidar cachés y nombrar cosas."*

---

## Por qué usar caché

```
Sin caché:
Request → Servidor → Base de datos → Procesar → Response
          ↑ cada request hace este recorrido completo

Con caché:
Request → [¿está en caché?] → Sí → Response inmediata (ms)
                             → No → Servidor → DB → Guardar en caché → Response
```

Impacto real:
- Reducir latencia de 200ms a <10ms
- Aliviar carga en la base de datos
- Escalar sin escalar la infraestructura

---

## Las cuatro decisiones de cualquier caché

1. **¿Qué guardar?** No todo merece ser cacheado. Ideal: datos costosos de generar, frecuentemente leídos y poco cambiantes.
2. **¿Dónde guardar?** En memoria (proceso), en un servicio externo (Redis), en el cliente (browser), en CDN, en el servidor (Next.js).
3. **¿Por cuánto tiempo?** TTL (Time To Live) — cuánto antes de considerar el dato obsoleto.
4. **¿Cuándo invalidar?** Por tiempo, por evento (el dato cambió), por tamaño (LRU).

---

## Estrategias principales

### Cache-Aside (Lazy Loading) — la más común

La app gestiona el caché explícitamente. Lee del caché; si no hay, va a la fuente y guarda:

```js
async function getUser(id) {
  // 1. Buscar en caché
  const cached = await redis.get(`user:${id}`);
  if (cached) return JSON.parse(cached);

  // 2. Cache miss — ir a la fuente
  const user = await db.user.findUnique({ where: { id } });
  if (!user) return null;

  // 3. Guardar en caché con TTL de 5 minutos
  await redis.setex(`user:${id}`, 300, JSON.stringify(user));

  return user;
}

// Invalidar cuando el usuario cambia
async function updateUser(id, data) {
  const user = await db.user.update({ where: { id }, data });
  await redis.del(`user:${id}`); // invalidar entrada específica
  return user;
}
```

**Ventajas:** simple, solo cachea lo que se pide. **Desventajas:** primera petición siempre es lenta (cache miss).

### Write-Through — escribir en caché y fuente al mismo tiempo

```js
async function updateUser(id, data) {
  // Actualizar en BD y caché simultáneamente
  const [user] = await Promise.all([
    db.user.update({ where: { id }, data }),
    redis.setex(`user:${id}`, 300, JSON.stringify({ id, ...data })),
  ]);
  return user;
}
```

**Ventajas:** el caché siempre está actualizado. **Desventajas:** escribe datos que quizás nunca se lean.

### Stale-While-Revalidate (SWR)

Sirve el dato cacheado (aunque esté desactualizado) y en segundo plano refresca el caché. Maximiza la velocidad percibida.

```js
async function getDataSWR(key, fetchFn, ttl = 60) {
  const cached = await redis.get(key);

  if (cached) {
    const { data, expiresAt } = JSON.parse(cached);

    // Si está caducado, revalidar en background sin bloquear
    if (Date.now() > expiresAt) {
      fetchFn().then(fresh => {
        redis.setex(key, ttl, JSON.stringify({
          data: fresh,
          expiresAt: Date.now() + ttl * 1000,
        }));
      });
    }

    return data; // devolver inmediatamente aunque esté stale
  }

  // Cache miss — bloquear y esperar
  const data = await fetchFn();
  await redis.setex(key, ttl, JSON.stringify({ data, expiresAt: Date.now() + ttl * 1000 }));
  return data;
}
```

Este es el modelo de **ISR (Incremental Static Regeneration) de Next.js**.

---

## Caché en Next.js App Router

Next.js implementa múltiples capas de caché sobre `fetch`:

```tsx
// Static — cacheado para siempre en build time (SSG)
const data = await fetch('/api/posts', { cache: 'force-cache' });

// Dynamic — sin caché, siempre fresco (SSR)
const data = await fetch('/api/posts', { cache: 'no-store' });

// ISR — revalidar cada N segundos (Stale-While-Revalidate)
const data = await fetch('/api/posts', { next: { revalidate: 60 } });

// Tag-based — revalidar por evento
const data = await fetch('/api/posts', { next: { tags: ['posts'] } });
```

```tsx
// Invalidación por evento desde un Server Action
import { revalidateTag, revalidatePath } from 'next/cache';

async function publishPost(id: string) {
  await db.post.update({ where: { id }, data: { published: true } });
  revalidateTag('posts');           // invalida todo lo tageado como 'posts'
  revalidatePath('/blog');          // invalida la ruta específica
}
```

---

## Problemas clásicos

### Cache Stampede (Thundering Herd)

Cuando el caché expira y cientos de requests llegan al mismo tiempo, todos van a la BD simultáneamente:

```js
// Solución: mutex/lock para que solo uno revalide
import Mutex from 'async-mutex';
const mutex = new Mutex();

async function getWithLock(key, fetchFn) {
  const cached = await redis.get(key);
  if (cached) return JSON.parse(cached);

  // Solo un proceso regenera el caché
  return mutex.runExclusive(async () => {
    const stillCached = await redis.get(key); // double-check
    if (stillCached) return JSON.parse(stillCached);

    const data = await fetchFn();
    await redis.setex(key, 300, JSON.stringify(data));
    return data;
  });
}
```

### Caché con datos sensibles

> [!WARNING]
> Nunca cachees datos específicos del usuario de forma compartida. Un caché de "el perfil del usuario" debe incluir el `userId` en la clave, o usar un caché privado (browser cache con `Cache-Control: private`).
>
> ```js
> // ❌ Todos los usuarios ven el mismo perfil
> redis.set('user-profile', JSON.stringify(profile));
>
> // ✅ Caché por usuario
> redis.set(`user-profile:${userId}`, JSON.stringify(profile));
> ```

---

## Cuándo NO usar caché

- Datos que cambian con cada request (saldos bancarios, inventario en tiempo real)
- Datos únicos por usuario con alta personalización
- Operaciones de escritura — el caché es para lecturas
- Cuando la coherencia es más importante que la velocidad

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
