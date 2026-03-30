---
title: "Tailwind CSS — Utilidades clave más allá del tutorial"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - tailwind
  - css
  - frontend
  - diseño
category: "01 Stack"
subcategory: "CSS-Tailwind"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[useState]]"
  - "[[App Router]]"
---

# Tailwind CSS — Utilidades clave más allá del tutorial

Tailwind es un framework CSS utility-first. En lugar de escribir clases semánticas como `.card` o `.btn-primary`, aplicas clases de utilidad directamente en el HTML. El resultado es CSS muy predecible y sin colisiones de nombres.

---

## Layout: Flexbox y Grid

### Flexbox

```html
<!-- Contenedor flex centrado, con gap y wrap -->
<div class="flex items-center justify-between gap-4 flex-wrap">
  <div>Item 1</div>
  <div>Item 2</div>
  <div class="flex-1">Item que ocupa el espacio restante</div>
</div>

<!-- Stack vertical con flex -->
<div class="flex flex-col gap-2">
  <div>Row 1</div>
  <div>Row 2</div>
</div>
```

### Grid

```html
<!-- Grid de 3 columnas iguales -->
<div class="grid grid-cols-3 gap-6">
  <div>Col 1</div>
  <div>Col 2</div>
  <div>Col 3</div>
</div>

<!-- Grid con columnas de ancho fijo y el resto auto -->
<div class="grid grid-cols-[280px_1fr] gap-4">
  <aside>Sidebar fijo</aside>
  <main>Contenido que crece</main>
</div>

<!-- Item que ocupa múltiples columnas -->
<div class="grid grid-cols-12 gap-4">
  <header class="col-span-12">Header completo</header>
  <aside class="col-span-3">Sidebar</aside>
  <main class="col-span-9">Contenido</main>
</div>
```

---

## Sistema de spacing

Tailwind usa una escala basada en rem donde cada unidad = 4px:

| Clase | Valor |
|-------|-------|
| `p-1` | 4px |
| `p-2` | 8px |
| `p-4` | 16px |
| `p-6` | 24px |
| `p-8` | 32px |
| `p-12` | 48px |
| `p-16` | 64px |

```html
<!-- Padding y margin -->
<div class="p-4">padding en todos los lados</div>
<div class="px-6 py-3">padding horizontal/vertical</div>
<div class="pt-4 pb-2">padding top/bottom independiente</div>
<div class="mt-8 mb-4">margin top/bottom</div>
<div class="mx-auto">centrado horizontal</div>

<!-- Espaciado entre children con space-* -->
<div class="flex space-x-4">
  <div>Item 1</div>
  <div>Item 2</div>  <!-- gap automático entre items -->
</div>
```

---

## Responsive design

Tailwind usa breakpoints móvil-primero. Sin prefijo = todos los tamaños; con prefijo = desde ese tamaño en adelante:

| Prefijo | Breakpoint |
|---------|-----------|
| (sin prefijo) | todos |
| `sm:` | ≥640px |
| `md:` | ≥768px |
| `lg:` | ≥1024px |
| `xl:` | ≥1280px |
| `2xl:` | ≥1536px |

```html
<!-- Una columna en móvil, dos en tablet, tres en desktop -->
<div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
  <!-- cards -->
</div>

<!-- Texto que aumenta con el viewport -->
<h1 class="text-2xl md:text-4xl lg:text-6xl font-bold">
  Título
</h1>

<!-- Ocultar/mostrar según breakpoint -->
<nav class="hidden lg:flex gap-4">
  <!-- Navegación desktop -->
</nav>
<button class="lg:hidden">
  <!-- Botón hamburguesa — solo mobile -->
</button>
```

---

## Dark mode

Con la estrategia `class` en `tailwind.config.js`:

```javascript
// tailwind.config.js
module.exports = {
  darkMode: 'class', // toggle manual con .dark en <html>
  // darkMode: 'media', // usa prefers-color-scheme del sistema
};
```

```html
<!-- Clases con prefijo dark: -->
<div class="bg-white dark:bg-gray-900 text-gray-900 dark:text-white">
  <p class="text-gray-600 dark:text-gray-300">Contenido</p>
  <button class="bg-blue-500 dark:bg-blue-400 hover:bg-blue-600 dark:hover:bg-blue-500">
    Acción
  </button>
</div>
```

```javascript
// Toggle en JavaScript
document.documentElement.classList.toggle('dark');

// Persistir en localStorage
const isDark = localStorage.getItem('theme') === 'dark';
if (isDark) document.documentElement.classList.add('dark');
```

---

## @apply — componentes reutilizables

Usa `@apply` para crear clases semánticas cuando el mismo conjunto de utilidades se repite mucho:

```css
/* globals.css */
@tailwind base;
@tailwind components;
@tailwind utilities;

@layer components {
  .btn-primary {
    @apply px-4 py-2 bg-blue-600 text-white rounded-lg
           hover:bg-blue-700 active:bg-blue-800
           disabled:opacity-50 disabled:cursor-not-allowed
           transition-colors font-medium text-sm;
  }

  .card {
    @apply bg-white dark:bg-gray-800 rounded-xl shadow-sm
           border border-gray-200 dark:border-gray-700 p-6;
  }

  .input-field {
    @apply w-full px-3 py-2 border border-gray-300 dark:border-gray-600
           rounded-lg bg-white dark:bg-gray-700
           text-gray-900 dark:text-white
           focus:outline-none focus:ring-2 focus:ring-blue-500
           placeholder:text-gray-400;
  }
}
```

> [!TIP]
> No abuses de `@apply`. Su mayor valor es en elementos que se repiten exactamente igual muchas veces (botones, inputs, badges). Para variaciones, sigue usando las clases directamente.

---

## Customización en tailwind.config.js

```javascript
// tailwind.config.js
const defaultTheme = require('tailwindcss/defaultTheme');

module.exports = {
  content: ['./src/**/*.{js,ts,jsx,tsx,astro}'],
  theme: {
    extend: {
      // Agregar colores de tu marca
      colors: {
        brand: {
          50: '#eff6ff',
          500: '#3b82f6',
          900: '#1e3a5f',
        },
      },
      // Fuentes con fallback
      fontFamily: {
        sans: ['Inter', ...defaultTheme.fontFamily.sans],
        mono: ['JetBrains Mono', ...defaultTheme.fontFamily.mono],
      },
      // Breakpoints personalizados
      screens: {
        xs: '475px',
        ...defaultTheme.screens,
      },
      // Animaciones
      animation: {
        'fade-in': 'fadeIn 0.3s ease-in-out',
        'slide-up': 'slideUp 0.4s ease-out',
      },
      keyframes: {
        fadeIn: { from: { opacity: '0' }, to: { opacity: '1' } },
        slideUp: {
          from: { transform: 'translateY(10px)', opacity: '0' },
          to: { transform: 'translateY(0)', opacity: '1' },
        },
      },
    },
  },
  plugins: [
    require('@tailwindcss/typography'),   // clase prose para contenido rich text
    require('@tailwindcss/forms'),         // reset de estilos de formularios
    require('@tailwindcss/aspect-ratio'), // utilidades aspect-ratio
  ],
};
```

---

## Componente real: Card de producto

```tsx
function ProductCard({ name, price, image, badge }: ProductCardProps) {
  return (
    <article className="group relative bg-white dark:bg-gray-800 rounded-2xl
                        overflow-hidden shadow-sm hover:shadow-md
                        border border-gray-100 dark:border-gray-700
                        transition-all duration-200">
      {badge && (
        <span className="absolute top-3 left-3 z-10 px-2 py-1
                         bg-red-500 text-white text-xs font-semibold rounded-full">
          {badge}
        </span>
      )}
      <div className="aspect-square overflow-hidden bg-gray-50">
        <img
          src={image}
          alt={name}
          className="w-full h-full object-cover group-hover:scale-105 transition-transform duration-300"
        />
      </div>
      <div className="p-4">
        <h3 className="font-semibold text-gray-900 dark:text-white truncate">{name}</h3>
        <p className="text-lg font-bold text-blue-600 dark:text-blue-400 mt-1">
          ${price.toFixed(2)}
        </p>
        <button className="btn-primary w-full mt-3">Agregar al carrito</button>
      </div>
    </article>
  );
}
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
