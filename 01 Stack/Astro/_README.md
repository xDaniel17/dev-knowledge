---
title: Astro - Static & Dynamic Content
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - astro
  - ssg
  - islands
  - frontend
  - static
category: "01 Stack"
subcategory: "Astro"
author: Daniel
version: 2.0.0
status: active
related: []
---

# Astro

Framework para construir sitios web rápidos con contenido estático o dinámico. Island Architecture.

---

## 📚 Documentación Completa

### Conceptos Fundamentales
[[Astro.md]] - Introducción a Astro
- Qué es Astro
- Island Architecture
- Zero JavaScript by default
- File-based routing
- Componentes

### Creación de Proyectos
[[Creación de proyecto.md]] - Iniciar proyectos Astro
- Instalación
- Estructura de carpetas
- Primeros pasos
- Configuración básica

### Conceptos Base
[[Conceptos base.md]] - Nociones fundamentales
- Astro files (.astro)
- Componentes
- Layouts
- Routing
- Assets

---

## 🔑 Conceptos Clave

### Island Architecture
```astro
<!-- Only this hydrates JavaScript -->
<Counter client:load />

<!-- This is pure HTML/CSS -->
<static-content />
```

### File-based Routing
```
src/pages/
├── index.astro          → /
├── about.astro          → /about
├── blog/
│   └── [slug].astro    → /blog/:slug
└── [...catchall].astro → /* (fallback)
```

### Astro Component
```astro
---
// Server-side code
const response = await fetch('...');
const data = await response.json();
---

<!-- Markup -->
<div class="card">
  {data.map(item => <p>{item}</p>)}
</div>

<style>
  /* Scoped CSS */
</style>
```

---

## 📊 Arquitectura Típica

```
astro-site/
├── src/
│   ├── pages/           # File-based routes
│   ├── layouts/         # Layout templates
│   ├── components/      # Reusable components
│   ├── content/         # Content collections
│   └── assets/          # Imágenes, fonts
├── astro.config.mjs     # Configuración
├── tsconfig.json
└── package.json
```

---

## 🚀 Use Cases

- **Blogs y Documentación** - Rápido, SEO-friendly
- **Sitios de Marketing** - Alto performance
- **Hybrid Static + Dynamic** - Contenido estático con islas dinámicas
- **Portfolio y Presentación** - Velocidad extrema

---

## 🔗 Enlaces Relacionados

- [[../Next.js/App Router.md|Next.js (Alternativa con SSR)]]
- [[../React/_README.md|React (Para componentes)]]
- [[../Web-Conceptos/Rendering-Strategies.md|Rendering Strategies]]
- [[../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
