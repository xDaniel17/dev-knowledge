---
title: Data Fetching en Next.js
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nextjs
  - data-fetching
  - server-components
  - caching
  - revalidation
category: "01 Stack"
subcategory: "Next.js"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 📡 Data Fetching en Next.js 14+

Estrategias modernas para obtener datos: desde el servidor, caché, revalidación y patrones avanzados.

---

## 🔑 Conceptos Clave

En Next.js 14+, el fetching ocurre directamente en Server Components usando la API `fetch` extendida:

```tsx
// Directamente en Server Components (async)
async function getData() {
  const res = await fetch('https://api.example.com/data');
  if (!res.ok) throw new Error('Failed to fetch');
  return res.json();
}

export default async function Page() {
  const data = await getData();
  return <div>{data.title}</div>;
}
```

---

## 📋 Tipos de Fetching

### 1. Static (SSG) — Cache Indefinido

**Mejor para:** Contenido que no cambia frecuentemente

```tsx
async function getPosts() {
  // Por defecto, Next.js cachea indefinidamente
  const res = await fetch('https://api.example.com/posts');
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();
  return posts.map(p => <p key={p.id}>{p.title}</p>);
}
```

**Cuándo se regenera:**
- En build time
- En demanda (revalidateOnDemand)
- Después del intervalo de revalidación

### 2. Revalidated (ISR) — Cache + Revalidación Periódica

**Mejor para:** Contenido que cambia con baja frecuencia

```tsx
const res = await fetch('https://api.example.com/posts', {
  next: { revalidate: 3600 }, // Revalidar cada 1 hora (en segundos)
});
```

**Flujo:**
```
1. Usuario solicita página
2. Servir versión cacheada (rápido)
3. Revalidar en segundo plano si pasó el tiempo
4. Próximo usuario recibe versión nueva
```

### 3. Dynamic (SSR) — Sin Caché

**Mejor para:** Contenido personalizado o en tiempo real

```tsx
const res = await fetch('https://api.example.com/user', {
  cache: 'no-store', // Siempre fresco, nunca cachea
});
```

O usa `export const dynamic = 'force-dynamic'`:

```tsx
export const dynamic = 'force-dynamic'; // Fuerza SSR para toda la ruta

async function getUser() {
  const res = await fetch('https://api.example.com/user');
  return res.json();
}

export default async function Page() {
  const user = await getUser();
  return <p>Hola, {user.name}</p>;
}
```

---

## 🔄 Revalidación

### On-Demand Revalidation

Regenerar caché cuando sucede un evento (sin esperar intervalo):

```tsx
// app/actions.ts
'use server';

import { revalidatePath, revalidateTag } from 'next/cache';

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string;

  // Crear en BD
  const newPost = await db.post.create({ data: { title } });

  // Revalidar ruta específica
  revalidatePath('/posts');
  
  // O revalidar por tag (más flexible)
  revalidateTag('posts');

  return newPost;
}
```

```tsx
// app/posts/page.tsx
async function getPosts() {
  const res = await fetch('https://api.example.com/posts', {
    next: { tags: ['posts'] }, // Etiqueta para revalidación
  });
  return res.json();
}

export default async function PostsPage() {
  const posts = await getPosts();
  return (
    <>
      <h1>Posts</h1>
      <NewPostForm onSuccess={() => {/* ya revalidado */}} />
      {posts.map(p => <p key={p.id}>{p.title}</p>)}
    </>
  );
}
```

### Segmentación de Revalidación

```tsx
// app/layout.tsx — revalidar cada 1 hora
export const revalidate = 3600;

// app/dashboard/page.tsx — revalidar cada 60 segundos
export const revalidate = 60;

// app/realtime/page.tsx — siempre dinámico
export const revalidate = 0;
```

---

## 🎯 Patrones de Fetching

### Pattern 1: Fetch en Servidor + Props a Cliente

```tsx
// app/page.tsx — Server Component
async function getInitialData() {
  const res = await fetch('https://api.example.com/initial');
  return res.json();
}

export default async function Page() {
  const initialData = await getInitialData();
  return <ClientComponent initialData={initialData} />;
}
```

```tsx
// app/components/ClientComponent.tsx
'use client';

import { useState } from 'react';

export function ClientComponent({ initialData }: any) {
  const [data, setData] = useState(initialData);

  const handleRefresh = async () => {
    const res = await fetch('/api/data');
    setData(await res.json());
  };

  return (
    <div>
      <p>{data.title}</p>
      <button onClick={handleRefresh}>Refrescar</button>
    </div>
  );
}
```

### Pattern 2: Progressive Enhancement con Server Actions

```tsx
// app/actions.ts
'use server';

export async function updateProfile(formData: FormData) {
  const name = formData.get('name');
  const email = formData.get('email');

  await db.user.update({
    where: { id: userId },
    data: { name, email },
  });

  revalidatePath('/profile');
  return { success: true };
}
```

```tsx
// app/profile/page.tsx
import { updateProfile } from '../actions';

export default function ProfilePage() {
  return (
    <form action={updateProfile}>
      <input name="name" />
      <input name="email" />
      <button type="submit">Guardar</button>
    </form>
  );
}
```

### Pattern 3: Polling con useEffect

Para datos que necesitan actualizarse en tiempo real:

```tsx
'use client';

import { useEffect, useState } from 'react';

export function LiveData() {
  const [data, setData] = useState(null);

  useEffect(() => {
    const interval = setInterval(async () => {
      const res = await fetch('/api/data');
      setData(await res.json());
    }, 5000); // Cada 5 segundos

    return () => clearInterval(interval);
  }, []);

  return <div>{data?.title}</div>;
}
```

### Pattern 4: Suspense + Fallback

```tsx
import { Suspense } from 'react';

async function PostContent() {
  const posts = await getPosts();
  return posts.map(p => <p key={p.id}>{p.title}</p>);
}

export default function PostsPage() {
  return (
    <>
      <h1>Posts</h1>
      <Suspense fallback={<p>Cargando posts...</p>}>
        <PostContent />
      </Suspense>
    </>
  );
}
```

---

## ⚙️ Configuración Avanzada

### Cookies y Headers

```tsx
import { cookies, headers } from 'next/headers';

async function getAuthenticatedData() {
  const cookieStore = cookies();
  const token = cookieStore.get('auth')?.value;

  const headersList = headers();
  const userAgent = headersList.get('user-agent');

  const res = await fetch('https://api.example.com/user', {
    headers: {
      'Authorization': `Bearer ${token}`,
    },
  });

  return res.json();
}
```

### Request Deduplication

Next.js automáticamente deduplica `fetch` requests idénticos durante el mismo render:

```tsx
// Ambas llamadas se deduplican automáticamente
const user = await fetch('/api/user');
const user2 = await fetch('/api/user'); // NO hace otra request

// Pero si usas diferentes opciones, se hace otra request
const user3 = await fetch('/api/user', { cache: 'no-store' });
```

### Streaming de Respuestas

Para respuestas grandes, usa streaming:

```tsx
export const experimental_ppr = true; // Partial Pre-Rendering

async function LargeDataset() {
  const data = await fetch('https://api.example.com/large', {
    next: { revalidate: 3600 },
  });
  
  return <DataRenderer data={data} />;
}

export default async function Page() {
  return (
    <>
      <h1>Dashboard</h1>
      <Suspense fallback={<p>Cargando datos grandes...</p>}>
        <LargeDataset />
      </Suspense>
    </>
  );
}
```

---

## 🚫 Antipatrones

### ❌ Fetching en Layout

```tsx
// ❌ MALO — el layout espera por el fetch
export default async function Layout({ children }) {
  const data = await fetch('/api/data'); // LENTO
  return <div>{children}</div>;
}

// ✅ MEJOR — layout rápido, fetch en page
export default function Layout({ children }) {
  return <div>{children}</div>;
}

// En page.tsx:
export default async function Page() {
  const data = await fetch('/api/data');
  return <div>{data.title}</div>;
}
```

### ❌ Múltiples Fetches Secuenciales

```tsx
// ❌ MALO — uno espera al otro
const user = await fetch('/api/user');
const posts = await fetch('/api/posts?userId=' + user.id);

// ✅ MEJOR — paralelizar
const [user, posts] = await Promise.all([
  fetch('/api/user'),
  fetch('/api/posts'),
]);
```

### ❌ Cambiar comportamiento de caché con headers

```tsx
// ❌ MALO
const res = await fetch('/api/data', {
  headers: { 'Cache-Control': 'no-cache' },
});

// ✅ BIEN — usa opciones de Next.js
const res = await fetch('/api/data', {
  cache: 'no-store',
});
```

---

## 📊 Tabla de Decisión

| Caso | Strategy | Revalidate | Ejemplo |
|------|----------|-----------|---------|
| Blog posts | Static (SSG) | `revalidate: 3600` | Blog público |
| Datos del usuario | Dynamic (SSR) | `cache: 'no-store'` | Dashboard |
| Productos catálogo | Revalidated | `revalidate: 600` | E-commerce |
| Notificaciones | Dynamic | `cache: 'no-store'` | Real-time |

---

## 🔗 Enlaces Relacionados

- [[App Router.md|App Router]]
- [[Server Actions.md|Server Actions]]
- [[Performance.md|Performance]]
- [[_README.md|Next.js Principal]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con patrones y ejemplos |
