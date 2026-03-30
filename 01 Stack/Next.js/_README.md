---
title: Next.js - React Framework
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nextjs
  - react
  - ssr
  - ssg
  - framework
category: "01 Stack"
subcategory: "Next.js"
author: Daniel
version: 2.0.0
status: active
related: []
---

# Next.js

Framework React con SSR, SSG, ISR y App Router. Producción-listo.

---

## 📚 Documentación Completa

### Guía Principal
[[Next.js.md]] - Introducción a Next.js
- Qué es Next.js
- Ventajas sobre React
- Rendering strategies
- File-based routing
- Configuración

### Setup y Creación
[[Creación-de-proyecto.md]] - Iniciar proyectos Next.js
- Create Next App
- Estructura de carpetas
- TypeScript setup
- Primeros pasos

### App Router
[[App Router.md]] - Nuevo sistema de routing (recomendado)
- File-based routing
- Server Components
- Client Components
- Layouts
- Metadata
- Streaming
- Route handlers (API)

### Guías Prácticas
[[Guías.md]] - Patrones y técnicas avanzadas
- Data fetching
- Static generation
- Server-side rendering
- Incremental Static Regeneration
- Image optimization
- Font optimization
- Deployment

---

## 🔑 Conceptos Clave

### App Router (nuevo, recomendado)
```
app/
├── layout.tsx          # Root layout
├── page.tsx            # / route
├── blog/
│   ├── layout.tsx     # Layout for blog
│   ├── page.tsx       # /blog
│   └── [slug]
│       └── page.tsx   # /blog/:slug
└── api/
    └── route.ts       # API endpoint
```

### Server Components (default)
```tsx
// app/posts/page.tsx - Renderiza en servidor
async function PostList() {
  const posts = await db.post.findMany();
  return <ul>{posts.map(p => <li>{p.title}</li>)}</ul>;
}
```

### Client Components
```tsx
'use client';

import { useState } from 'react';

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>{count}</button>;
}
```

### Static vs Dynamic
```tsx
// Static (genera en build)
export default async function Page() {
  const data = await fetch('...', { cache: 'force-cache' });
}

// Dynamic (genera en request)
export const dynamic = 'force-dynamic';
```

---

## 📊 Rendering Strategies

| Strategy | When | Best For |
|----------|------|----------|
| **SSG** | Build time | Contenido estático, blogs |
| **ISR** | Build + revalidate | Contenido semi-estático |
| **SSR** | On-request | Contenido dinámico, personalizacion |
| **CSR** | Browser | Interactividad, real-time |

---

## 🏗️ Arquitectura Típica

```
nextjs-app/
├── app/
│   ├── layout.tsx          # Root layout
│   ├── page.tsx            # Home page
│   ├── (auth)/             # Route group
│   │   ├── login/page.tsx
│   │   └── register/page.tsx
│   ├── blog/
│   │   ├── page.tsx        # /blog
│   │   ├── [slug]/page.tsx # /blog/:slug
│   │   └── layout.tsx
│   └── api/
│       └── route.ts        # API endpoints
├── components/             # Componentes reutilizables
├── lib/                    # Utilidades
├── styles/
└── public/
```

---

## 🚀 Deployment

- **Vercel** - Plataforma oficial (recomendada)
- **Self-hosted** - Node.js server
- **Docker** - Containerización
- **Static export** - Pure HTML/CSS

---

## 🔗 Enlaces Relacionados

- [[../React/_README.md|React Core]]
- [[../Web-Conceptos/Rendering-Strategies.md|Rendering Strategies]]
- [[../Astro/_README.md|Astro (Alternativa)]]
- [[../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
