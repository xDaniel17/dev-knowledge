---
title: Rendering Strategies - SSR, SSG, CSR
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/Web-Conceptos/Rendering-Strategies.md
---

# 🎨 Rendering Strategies

## ¿Dónde se renderiza HTML?

### Server-Side Rendering (SSR)
**El servidor genera HTML, lo envía al cliente**

```
Navegador → Servidor → HTML + CSS + JS → Navegador renderiza
```

#### Ventajas
- ✅ SEO excelente
- ✅ Rápido para First Contentful Paint (FCP)
- ✅ Contenido siempre actualizado
- ✅ Compatible con navegadores antiguos

#### Desventajas
- ❌ Carga en servidor
- ❌ Latencia de red
- ❌ Requiere esperar HTML completo

#### Ejemplos
- Next.js (modo SSR)
- Astro (defecto)
- Nuxt.js
- Remix

---

### Client-Side Rendering (CSR)
**El navegador descarga JS y genera HTML**

```
Navegador descarga JS → JS se ejecuta → Genera HTML → Renderiza
```

#### Ventajas
- ✅ Experiencia más rápida después del primer load
- ✅ Menos carga en servidor
- ✅ Interactividad inmediata
- ✅ Fácil de escalar

#### Desventajas
- ❌ SEO deficiente
- ❌ JavaScript crítico en el path
- ❌ Blanco inicial (white flash)
- ❌ Mayor consumo de batería en móviles

#### Ejemplos
- React (SPA)
- Vue.js (SPA)
- Angular (SPA)
- Svelte (SPA)

---

### Static Site Generation (SSG)
**Generación de HTML en tiempo de build**

```
Build time: Generar HTML estático → Deploy → Servir archivos estáticos
```

#### Ventajas
- ✅ Rendimiento máximo
- ✅ Sin carga en servidor
- ✅ SEO perfecto
- ✅ Muy seguro
- ✅ Barato de hostear (CDN)

#### Desventajas
- ❌ No funciona para contenido dinámico
- ❌ Tiempo de build puede ser largo
- ❌ Rebuild necesario para actualizaciones

#### Ejemplos
- Gatsby
- Hugo
- Jekyll
- Astro (modo SSG)
- Next.js (modo SSG)

---

## Comparativa

| Métrica | SSR | CSR | SSG |
|---------|-----|-----|-----|
| **Performance** | 🟡 Bueno | 🔴 Lento | 🟢 Excelente |
| **SEO** | 🟢 Excelente | 🔴 Deficiente | 🟢 Excelente |
| **Escalabilidad** | 🔴 Difícil | 🟢 Fácil | 🟢 Fácil |
| **Complejidad** | 🟡 Media | 🟢 Baja | 🟡 Media |
| **Dinamismo** | 🟢 Sí | 🟢 Sí | 🔴 No |
| **Costo hosting** | 🔴 Alto | 🟡 Medio | 🟢 Bajo |

---

## Estrategias modernas

### Hybrid Rendering
Combinar SSG + SSR + CSR según necesidad:

```
Página estática → SSG
Página con datos actualizados → SSR/ISR
Componentes interactivos → CSR
```

### Incremental Static Regeneration (ISR)
Regenerar páginas estáticas en tiempo de ejecución:

```
Next.js: revalidate: 60  # Regenerar cada 60 segundos
```

### Edge Computing
Ejecutar código en servidores cercanos al usuario (Cloudflare Workers, Vercel Edge)

---

## ¿Cuál elegir?

### SSG
- Blog, landing pages, documentación
- Contenido que no cambia frecuentemente

### SSR
- E-commerce, redes sociales
- Contenido personalizado por usuario
- Datos que cambian constantemente

### CSR
- Aplicaciones internas
- Herramientas administrativas
- Experiencias muy interactivas

### Hybrid
- Mayoría de aplicaciones modernas
- Combinar lo mejor de cada uno

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documento inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
