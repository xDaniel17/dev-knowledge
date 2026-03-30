---
title: "Astro — Conceptos base y arquitectura"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - astro
  - frontend
  - performance
  - static-site
category: "01 Stack"
subcategory: "Astro"
author: Daniel
version: 1.0.0
status: active
related: []
---

# Astro — Conceptos base y arquitectura

Astro es un framework web enfocado en performance que genera HTML estático por defecto, sin JavaScript en el cliente. Permite usar componentes de React, Vue o Svelte y solo envía JS cuando un componente específicamente lo necesita.

---

## ¿Para qué sirve Astro?

Astro brilla en sitios con contenido mayormente estático: blogs, documentación, portfolios, landing pages, sitios de marketing. La premisa es: si el contenido no cambia con la interacción del usuario, no envíes JavaScript.

**Cuándo elegir Astro:**
- Sitio de contenido: blog, docs, portfolio, marketing
- SEO crítico y velocidad de carga prioritaria
- Contenido viene de Markdown, CMS headless o APIs
- Quieres usar componentes de distintos frameworks en un mismo sitio

**Cuándo NO elegir Astro:**
- App con mucha interactividad (dashboards, apps de gestión, SaaS complejos)
- Necesitas estado global cliente complejo
- La mayor parte del contenido es dinámico y específico del usuario en tiempo real

---

## Island Architecture

El concepto central de Astro. La página es HTML estático (el "océano"), y las partes interactivas son "islas" de JavaScript que se hidratan de forma independiente.

```
+----------------------------------+
|  Header (HTML estático)          |
+----------------------------------+
|  Hero (HTML estático)            |
+----------------------------------+
|  +----------------------------+  |
|  | Carrusel (isla React 🏝️)   |  |
|  +----------------------------+  |
+----------------------------------+
|  Blog posts (HTML estático)      |
+----------------------------------+
|  +----------------------------+  |
|  | Comentarios (isla Vue 🏝️)  |  |
|  +----------------------------+  |
+----------------------------------+
```

Las islas se cargan de forma independiente y no bloquean el resto de la página.

---

## Sintaxis de .astro

Un archivo `.astro` tiene tres secciones separadas por `---`:

```astro
---
// Frontmatter — se ejecuta en el servidor en build time (o SSR)
// Aquí puedes hacer fetch, importar componentes, procesar datos
import Layout from '../layouts/Layout.astro';
import Card from '../components/Card.astro';

const response = await fetch('https://api.example.com/posts');
const posts = await response.json();

const title = 'Mi Blog';
---

<!-- Template HTML — acepta expresiones JSX-like -->
<Layout title={title}>
  <h1>{title}</h1>

  {posts.map(post => (
    <Card title={post.title} url={`/posts/${post.slug}`} />
  ))}
</Layout>

<style>
  /* Scoped por defecto — no afecta a otros componentes */
  h1 {
    color: rebeccapurple;
  }
</style>
```

---

## Directivas de cliente (client:*)

Para hidratar un componente de framework, usas una directiva `client:*`:

```astro
---
import Counter from '../components/Counter.jsx'; // Componente React
import Carousel from '../components/Carousel.vue'; // Componente Vue
---

<!-- Sin directiva: HTML estático, cero JS enviado -->
<Counter />

<!-- client:load: hidrata inmediatamente al cargar la página -->
<Counter client:load />

<!-- client:idle: hidrata cuando el browser está idle -->
<Counter client:idle />

<!-- client:visible: hidrata cuando el elemento entra en viewport -->
<Carousel client:visible />

<!-- client:media: hidrata según media query -->
<MobileMenu client:media="(max-width: 768px)" />

<!-- client:only: renderiza solo en cliente, sin SSR -->
<MapWidget client:only="react" />
```

> [!TIP]
> Prefiere `client:idle` o `client:visible` sobre `client:load` cuando no es necesario interactividad inmediata. Mejora significativamente el LCP y TTI.

---

## Content Collections

Sistema tipado para gestionar colecciones de contenido Markdown/MDX:

```typescript
// src/content/config.ts
import { defineCollection, z } from 'astro:content';

const blog = defineCollection({
  type: 'content', // Markdown o MDX
  schema: z.object({
    title: z.string(),
    date: z.date(),
    tags: z.array(z.string()),
    draft: z.boolean().default(false),
    image: z.string().optional(),
  }),
});

export const collections = { blog };
```

```astro
---
// src/pages/blog/[slug].astro
import { getCollection, getEntry } from 'astro:content';

// Obtener todos los posts (filtrar drafts en producción)
export async function getStaticPaths() {
  const posts = await getCollection('blog', ({ data }) => !data.draft);
  return posts.map(post => ({
    params: { slug: post.slug },
    props: { post },
  }));
}

const { post } = Astro.props;
const { Content } = await post.render();
---

<article>
  <h1>{post.data.title}</h1>
  <Content />
</article>
```

---

## Integraciones y adaptadores

```bash
# Agregar integración de React
npx astro add react

# Agregar Tailwind
npx astro add tailwind

# Agregar adaptador para SSR en Vercel
npx astro add vercel

# Agregar MDX
npx astro add mdx
```

Para habilitar SSR (en lugar de solo static):

```javascript
// astro.config.mjs
import { defineConfig } from 'astro/config';
import vercel from '@astrojs/vercel/serverless';

export default defineConfig({
  output: 'server', // 'static' (default) | 'server' | 'hybrid'
  adapter: vercel(),
});
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
