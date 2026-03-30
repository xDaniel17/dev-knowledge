---
title: Performance y Optimization en Next.js
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nextjs
  - performance
  - optimization
  - web-vitals
  - images
  - bundling
category: "01 Stack"
subcategory: "Next.js"
author: Daniel
version: 1.0.0
status: active
related: []
---

# ⚡ Performance y Optimization en Next.js 14+

Estrategias para mantener tu aplicación rápida: imágenes, bundling, hydration, y Core Web Vitals.

---

## 🎯 Core Web Vitals

Google mide la experiencia de usuario con tres métricas:

| Métrica | Bueno | Necesita mejora | Pobre |
|---------|-------|----------------|----|
| **LCP** (Largest Contentful Paint) | < 2.5s | 2.5s - 4s | > 4s |
| **FID** (First Input Delay) | < 100ms | 100ms - 300ms | > 300ms |
| **CLS** (Cumulative Layout Shift) | < 0.1 | 0.1 - 0.25 | > 0.25 |

---

## 🖼️ Image Optimization

### Next.js Image Component

```tsx
import Image from 'next/image';

// ❌ HTML nativo (lento)
<img src="/photo.jpg" alt="Photo" />

// ✅ Next.js Image (optimizado)
<Image
  src="/photo.jpg"
  alt="Photo"
  width={800}
  height={600}
  placeholder="blur"
  blurDataURL="data:image/..." // LQIP
/>
```

**Beneficios:**
- Lazy loading automático
- Responsive images (diferentes sizes para diferentes viewports)
- WebP moderno (fallback a JPEG)
- Prevención de layout shift

### Imágenes Responsivas

```tsx
<Image
  src="/photo.jpg"
  alt="Photo"
  width={800}
  height={600}
  sizes="(max-width: 640px) 100vw,
         (max-width: 1024px) 50vw,
         800px"
  priority={false} // true para above-the-fold
/>
```

### Image Gallery

```tsx
import Image from 'next/image';

export function Gallery({ images }: { images: Image[] }) {
  return (
    <div className="grid grid-cols-3 gap-4">
      {images.map((img, i) => (
        <Image
          key={img.id}
          src={img.src}
          alt={img.alt}
          width={300}
          height={300}
          priority={i < 3} // Prioritizar primeras 3
          sizes="(max-width: 768px) 100vw, 33vw"
        />
      ))}
    </div>
  );
}
```

---

## 📦 Code Splitting y Bundle Optimization

### Dynamic Imports

Cargar componentes bajo demanda:

```tsx
import dynamic from 'next/dynamic';

const HeavyComponent = dynamic(() => import('./HeavyComponent'), {
  loading: () => <p>Cargando...</p>,
  ssr: false, // Opcional: no renderizar en servidor
});

export default function Page() {
  return <HeavyComponent />;
}
```

### Route-based Splitting

Next.js automáticamente divide el código por ruta:

```
.next/static/chunks/
├── pages-index-abc123.js     (página /)
├── pages-about-def456.js     (página /about)
├── main-ghi789.js            (shared)
└── ...
```

Cada página solo carga su código necesario.

### Component Splitting

```tsx
// ❌ Bundle grande
export function Dashboard() {
  return (
    <div>
      <HeavyChart /> {/* 500KB */}
      <ComplexForm /> {/* 300KB */}
      <LargeTable /> {/* 200KB */}
    </div>
  );
}

// ✅ Dividido y lazy-loaded
const HeavyChart = dynamic(() => import('./HeavyChart'));
const ComplexForm = dynamic(() => import('./ComplexForm'));
const LargeTable = dynamic(() => import('./LargeTable'));

export function Dashboard() {
  const [activeTab, setActiveTab] = useState('chart');

  return (
    <div>
      {activeTab === 'chart' && <HeavyChart />}
      {activeTab === 'form' && <ComplexForm />}
      {activeTab === 'table' && <LargeTable />}
    </div>
  );
}
```

---

## 🚀 Server Components para Performance

### Problema: JavaScript Excesivo

```tsx
// ❌ MALO - 150KB de JS para un formulario
'use client';

import { useState } from 'react';
import heavyLibrary from 'heavy-lib'; // 100KB

export function BlogPost({ content }) {
  const [likes, setLikes] = useState(0);

  return (
    <article>
      <h1>{content.title}</h1>
      <p>{content.body}</p>
      <button onClick={() => setLikes(likes + 1)}>
        {likes} Likes
      </button>
    </article>
  );
}
```

### Solución: Hybrid Components

```tsx
// app/blog/[id]/page.tsx — Server Component
async function BlogPostPage({ params }: { params: { id: string } }) {
  const post = await getPost(params.id); // Datos desde servidor

  return (
    <article>
      <h1>{post.title}</h1>
      <p>{post.body}</p>
      <LikeButton postId={post.id} /> {/* Solo esta parte es cliente */}
    </article>
  );
}

// components/LikeButton.tsx — Client Component (pequeño)
'use client';

import { useState } from 'react';

export function LikeButton({ postId }: { postId: string }) {
  const [likes, setLikes] = useState(0);

  return (
    <button onClick={() => setLikes(likes + 1)}>
      {likes} Likes
    </button>
  );
}
```

---

## 🔄 Hydration Optimization

### Problema: Hydration Mismatch

```tsx
// ❌ MALO
export default function Clock() {
  const [time, setTime] = useState(new Date()); // Diferente en cliente

  return <div>{time.toLocaleTimeString()}</div>;
}
```

### Solución: suppressHydrationWarning

```tsx
// ✅ BIEN para contenido dinámico
export default function Clock() {
  const [mounted, setMounted] = useState(false);

  useEffect(() => setMounted(true), []);

  if (!mounted) return <div>--:--:--</div>;

  return <div>{new Date().toLocaleTimeString()}</div>;
}
```

---

## 📝 Font Optimization

### Google Fonts

```tsx
// app/layout.tsx
import { Inter, Playfair_Display } from 'next/font/google';

const inter = Inter({ subsets: ['latin'] });
const playfair = Playfair_Display({ subsets: ['latin'] });

export default function RootLayout({ children }) {
  return (
    <html lang="en" className={inter.className}>
      <body>
        <h1 className={playfair.className}>Título</h1>
        {children}
      </body>
    </html>
  );
}
```

**Beneficios:**
- Descargado en build time (sin redirect)
- Self-hosted (más rápido)
- CSS variables automático

---

## 🔍 Monitoring con Vercel Analytics

```tsx
// app/layout.tsx
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
      </body>
    </html>
  );
}
```

---

## 🚫 Antipatrones de Performance

### ❌ No usar Image Component

```tsx
// ❌ MALO
<img src="/large-photo.jpg" alt="Photo" />

// ✅ BIEN
import Image from 'next/image';
<Image src="/large-photo.jpg" alt="Photo" width={800} height={600} />
```

### ❌ Fetch en Layout

```tsx
// ❌ MALO
export default async function Layout({ children }) {
  const globalData = await fetch('/api/config'); // Bloquea todo
  return <div>{children}</div>;
}

// ✅ BIEN
export default function Layout({ children }) {
  return <div>{children}</div>;
}

// En page.tsx:
export default async function Page() {
  const data = await fetch('/api/config');
  return <div>{data.title}</div>;
}
```

### ❌ useEffect para data fetching

```tsx
// ❌ MALO
'use client';
export function UserProfile({ userId }) {
  const [user, setUser] = useState(null);

  useEffect(() => {
    fetch(`/api/users/${userId}`).then(setUser); // Late, no SSR
  }, [userId]);

  return <div>{user?.name}</div>;
}

// ✅ BIEN
async function UserProfile({ userId }) {
  const user = await fetch(`/api/users/${userId}`); // SSR, rápido
  return <div>{user.name}</div>;
}
```

### ❌ Scripts sin control

```tsx
// ❌ MALO
export default function Layout({ children }) {
  return (
    <html>
      <body>
        {children}
        <script src="https://cdn.analytics.com/tracking.js" />
        <script src="https://cdn.ads.com/ads.js" />
      </body>
    </html>
  );
}

// ✅ BIEN
import Script from 'next/script';

export default function Layout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Script
          src="https://cdn.analytics.com/tracking.js"
          strategy="lazyOnload" // Cargar después de interacción
        />
        <Script
          src="https://cdn.ads.com/ads.js"
          strategy="afterInteractive"
        />
      </body>
    </html>
  );
}
```

---

## 📊 Monitoring Bundle Size

```bash
# Analizar bundle
npm run build

# Output:
# Page                                       Size      First Load JS
# ┌ ○ /                                      45 kB            87 kB
# ├ ○ /api/posts                            0 B              87 kB
# ├ ○ /posts                                23 kB           108 kB
# └ ○ /admin                                45 kB           132 kB
```

### Herramientas

- **Vercel Analytics** - Monitoreo en producción
- **bundle-analyzer** - Analizar bundle localmente
- **lighthouse** - Auditorías de performance

---

## ✅ Checklist de Performance

- [ ] Usar `Image` en lugar de `<img>`
- [ ] Optimizar fonts (Google Fonts integrado)
- [ ] Server Components por defecto, Client solo donde sea necesario
- [ ] Dynamic imports para componentes pesados
- [ ] Monitorear Core Web Vitals
- [ ] Lazy load scripts third-party
- [ ] Implement caching adecuadamente
- [ ] Reducir JavaScript inicial
- [ ] Usar Suspense para loading states
- [ ] Auditar con Lighthouse regularmente

---

## 🔗 Enlaces Relacionados

- [[App Router.md|App Router]]
- [[Data-Fetching.md|Data Fetching]]
- [[Server-Actions.md|Server Actions]]
- [[_README.md|Next.js Principal]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con estrategias y ejemplos |
