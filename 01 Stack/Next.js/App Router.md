---
title: "App Router — Next.js 14+"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nextjs
  - app-router
  - react
  - server-components
category: "01 Stack"
subcategory: "Next.js"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[useState]]"
  - "[[Event Loop]]"
---

# App Router — Next.js 14+

El App Router es el sistema de routing moderno de Next.js, introducido en v13 y estabilizado en v14. Reemplaza al Pages Router y trae Server Components, Server Actions y un nuevo modelo mental para el rendering.

---

## Pages Router vs App Router

| Característica | Pages Router | App Router |
|---|---|---|
| Directorio | `pages/` | `app/` |
| Rendering por defecto | Client-side | Server-side |
| Layouts | `_app.tsx` | `layout.tsx` anidados |
| Loading states | Manual | `loading.tsx` |
| Error boundaries | Manual | `error.tsx` |
| Data fetching | `getServerSideProps` / `getStaticProps` | `fetch` directo en componentes |
| Mutations | API Routes | Server Actions |

---

## Estructura de carpetas

```
app/
├── layout.tsx          # Layout raíz — envuelve toda la app
├── page.tsx            # Ruta: /
├── loading.tsx         # UI de carga para esta ruta
├── error.tsx           # Error boundary para esta ruta
├── not-found.tsx       # Página 404
├── globals.css
├── dashboard/
│   ├── layout.tsx      # Layout anidado — solo para /dashboard/*
│   ├── page.tsx        # Ruta: /dashboard
│   └── settings/
│       └── page.tsx    # Ruta: /dashboard/settings
└── api/
    └── users/
        └── route.ts    # API Route Handler: /api/users
```

Los nombres de archivo tienen significado especial en el App Router. `page.tsx` define una ruta pública. `layout.tsx` persiste entre navegaciones y no se re-monta.

---

## Server Components vs Client Components

Por defecto, todos los componentes en `app/` son **Server Components**. Se renderizan en el servidor y el HTML resultante se envía al cliente. No pueden tener estado, efectos ni event handlers.

```tsx
// app/page.tsx — Server Component por defecto
// No necesita 'use client', se ejecuta en el servidor

async function HomePage() {
  // Fetch directo — esto corre en el servidor, no expone secrets al cliente
  const data = await fetch('https://api.example.com/posts', {
    cache: 'force-cache', // equivale a getStaticProps
  });
  const posts = await data.json();

  return (
    <main>
      {posts.map((post: any) => (
        <article key={post.id}>{post.title}</article>
      ))}
    </main>
  );
}
```

Para interactividad (useState, useEffect, event handlers), se necesita `'use client'`:

```tsx
'use client';

import { useState } from 'react';

export function LikeButton({ postId }: { postId: string }) {
  const [liked, setLiked] = useState(false);

  return (
    <button onClick={() => setLiked(!liked)}>
      {liked ? '❤️' : '🤍'}
    </button>
  );
}
```

> [!IMPORTANT]
> El marcador `'use client'` define un límite. El componente y todos sus imports se convierten en código cliente. Coloca los Client Components lo más abajo posible en el árbol para maximizar el rendering en servidor.

---

## Fetch con cache y revalidación

Next.js extiende la API nativa `fetch` con opciones de caché:

```tsx
// Equivalente a getStaticProps — cacheado indefinidamente
const data = await fetch('/api/posts', { cache: 'force-cache' });

// Equivalente a getServerSideProps — siempre fresco
const data = await fetch('/api/posts', { cache: 'no-store' });

// ISR — revalidar cada 60 segundos
const data = await fetch('/api/posts', { next: { revalidate: 60 } });

// Revalidar bajo demanda (con tags)
const data = await fetch('/api/posts', { next: { tags: ['posts'] } });
// En un Server Action:
import { revalidateTag } from 'next/cache';
revalidateTag('posts');
```

---

## Server Actions

Los Server Actions permiten ejecutar código servidor desde el cliente sin crear API routes manualmente:

```tsx
// app/actions.ts
'use server';

import { revalidatePath } from 'next/cache';

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string;

  await db.post.create({ data: { title } });

  revalidatePath('/posts'); // actualiza la caché de esa ruta
}
```

```tsx
// app/new-post/page.tsx
import { createPost } from '../actions';

export default function NewPostPage() {
  return (
    <form action={createPost}>
      <input name="title" placeholder="Título" />
      <button type="submit">Crear</button>
    </form>
  );
}
```

---

## Layouts anidados

Los layouts se anidan automáticamente. El layout raíz envuelve todo; los layouts de segmento solo se aplican a sus rutas:

```tsx
// app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="es">
      <body>
        <nav>Navegación global</nav>
        {children}
      </body>
    </html>
  );
}

// app/dashboard/layout.tsx — anidado dentro del root layout
export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <div className="dashboard-wrapper">
      <aside>Sidebar del dashboard</aside>
      <main>{children}</main>
    </div>
  );
}
```

---

## loading.tsx y error.tsx

```tsx
// app/dashboard/loading.tsx — se muestra mientras la page.tsx carga
export default function Loading() {
  return <div>Cargando dashboard...</div>;
}

// app/dashboard/error.tsx — error boundary automático
'use client'; // error.tsx siempre debe ser Client Component

export default function Error({
  error,
  reset,
}: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  return (
    <div>
      <h2>Algo salió mal</h2>
      <button onClick={() => reset()}>Intentar de nuevo</button>
    </div>
  );
}
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
