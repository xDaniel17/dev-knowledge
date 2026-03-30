---
title: "01 Stack — Mapa de tecnologías"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - stack
  - meta
  - indice
category: "01 Stack"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# 01 Stack

Documentación técnica de las tecnologías del stack de trabajo. Una subcarpeta por tecnología, máximo un nivel de profundidad.

---

## Mapa de subcarpetas

### Frontend
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `React/` | Framework declarativo + Hooks completos | [[React/_README.md|18+ Hooks, Catálogo, Comparativa, Patrones]] |
| `Next.js/` | React meta-framework (SSR, SSG, ISR) | [[Next.js/_README.md|App Router, Data Fetching, Server Actions, Performance, Deployment]] |
| `Angular/` | Framework enterprise TypeScript-first | [[Angular/_README.md|Signals, Directives, RxJS, Components, Setup]] |
| `Astro/` | Island architecture, SSG/SSR híbrido | [[Astro/_README.md|File-based routing, Zero JS by default, Setup]] |
| `CSS-Tailwind/` | Utility-first CSS framework | [[CSS-Tailwind/Utilidades clave.md|Responsive, Dark mode, Customización]] |

### Backend
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `Node/` | JavaScript runtime para servidor | [[Node/_README.md|Event Loop, Instalación oficial, fnm, Guías]] |
| `Express/` | Minimal web framework | [[Express/_README.md|Middlewares, Routing, Error Handling, REST APIs]] |
| `Strapi/` | Headless CMS basado en Node | [[Strapi/_README.md|Content Types, Auto-generated APIs, Estructura]] |

### Fundamentos Web
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `Web-Conceptos/` | Conceptos teóricos y arquitectónicos | [[Web-Conceptos/_README.md|REST API, GraphQL, Rendering Strategies, Arquitectura Web]] |

### IA & Automatización
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `IA-Agents/` | Agentes, MCP, Chain of Thought | [[IA-Agents/_README.md|Agentes-IA, MCP Protocol, Skills Ecosystem]] |
| `IA-Tools/` | Herramientas de IA para desarrollo | [[IA-Tools/_README.md|OpenCode, Claude Code, GitHub Copilot]] |
| `IA-Skills/` | Sistema de skills y extensiones | [[IA-Skills/_README.md|Skills, MCP Anthropic, Vercel, Skills.sh]] |

### Plataformas y Servicios
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `Tech-Services/` | Hosting, bases de datos, servicios cloud | [[Tech-Services/_README.md|Vercel, Netlify, MongoDB, Firebase, PostgreSQL-Supabase, Redis]] |

### DevOps & Herramientas
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `Git/` | Control de versiones distribuido | [[Git/_README.md|Comandos, Workflows, Git Flow, GitHub Flow]] |
| **CLI-Reference** | Comandos terminal Windows y macOS | [[CLI-Reference.md|npm, git, Node.js, Netlify, Vercel, MongoDB]] |
| **Docker** | Containerización y composición | [[Docker.md|Dockerfile, Compose, Multi-stage, Optimización]] |
| **TypeScript** | Tipado estático para JavaScript | [[TypeScript.md|Tipos, Generics, Utilidades, React integration]] |
| **Testing** | Jest, Cypress, React Testing Library | [[Testing.md|Unit, Integration, E2E, Mocking, Coverage]] |
| **fnm-Homebrew** | Gestión de Node y paquetes | [[fnm-Homebrew.md|fnm, Homebrew, .nvmrc, Setup]] |

---

## Notas por subcategoría

```dataview
TABLE title, subcategory, lastUpdated
FROM "01 Stack"
WHERE file.name != "_README"
SORT subcategory ASC, lastUpdated DESC
```

---

## 📊 Estadísticas de Documentación

- **Total de carpetas**: 15+ (incluyendo Tech-Services, DevOps)
- **Total de archivos**: 130+ (recuperados, mejorados y nuevos)
- **READMEs por carpeta**: 13+ (navegación mejorada)
- **Documentación de React Hooks**: 18+ hooks cubiertos
- **Documentación de Next.js**: 7+ guías especializadas
- **Documentación de Servicios**: Vercel, MongoDB, Netlify, Firebase, PostgreSQL-Supabase, Redis
- **Documentación de Backend**: Firebase, PostgreSQL, Supabase, Redis, MongoDB
- **Documentación de Testing**: Jest, Cypress, React Testing Library
- **Documentación de DevOps**: Docker, TypeScript, Testing, fnm-Homebrew
- **CLI Reference**: Comandos Windows y macOS
- **Últimas actualizaciones**: 2026-03-30

---

## 🎯 Navegación Rápida

### Por Nivel
- **Principiantes**: [[React/_README.md|React Basics]], [[Web-Conceptos/_README.md|Web Conceptos]], [[CLI-Reference.md|CLI Commands]]
- **Intermedio**: [[Next.js/_README.md|Next.js]], [[Express/_README.md|Express]], [[Git/_README.md|Git]], [[Tech-Services/MongoDB.md|MongoDB]]
- **Avanzado**: [[Angular/_README.md|Angular]], [[IA-Agents/_README.md|IA Agents]], [[Node/_README.md|Node internals]], [[Next.js/Performance.md|Performance]]

### Por Caso de Uso
- **Frontend moderno**: React, Next.js, Angular, Astro
- **Backend robusto**: Node.js, Express, Strapi
- **Bases de datos**: MongoDB, Prisma, Mongoose
- **Deployment**: Vercel, Netlify, Docker
- **IA y Automatización**: IA-Agents, IA-Tools, IA-Skills
- **Teoría Web**: Web-Conceptos, Rendering Strategies
- **DevOps & Terminal**: Git, CLI-Reference

---

## ✨ Mejoras Recientes

### Versión 4.0.0 (2026-03-30)
- ✅ **Firebase documentation**: Autenticación, Firestore, Cloud Functions, Storage
- ✅ **PostgreSQL & Supabase**: SQL avanzado, RLS, Supabase client, real-time
- ✅ **Redis Caching**: Estrategias, sessionmanagement, pub/sub, optimización
- ✅ **TypeScript Best Practices**: Tipos avanzados, Generics, Utilidades, React integration
- ✅ **Testing Strategy**: Jest, Cypress, React Testing Library, E2E, Mocking
- ✅ **Docker & Containerization**: Dockerfile, Compose, Multi-stage, Security, Optimization
- ✅ **Tech-Services actualizado**: 6 servicios principales documentados

### Versión 3.0.0 (2026-03-30)
- ✅ **React Hooks expandido**: Catálogo de 18+ hooks + comparativa
- ✅ **Next.js profundizado**: Data Fetching, Server Actions, Performance, Deployment
- ✅ **Vercel documentation**: Setup, env vars, Edge Functions, analytics
- ✅ **MongoDB documentation**: Setup local, Mongoose, Prisma, MongoDB Atlas
- ✅ **Netlify documentation**: Functions, Forms, Split testing, deployment
- ✅ **CLI Reference**: Comandos completos para Windows y macOS
- ✅ **Tech-Services carpeta**: Centralizar plataformas y servicios

### Versión 2.0.0 (2026-03-30)
- ✅ **Recuperados 80+ archivos** de bóveda anterior
- ✅ **React Hooks completo** (useState, useEffect, useContext, useReducer, etc.)
- ✅ **Web Conceptos fundamentales** (REST, GraphQL, Arquitectura, Rendering)
- ✅ **IA-Agents enriquecido** (MCP, Agentes, Skills Ecosystem)
- ✅ **READMEs de navegación** en todas las carpetas principales
- ✅ **Documentación de frameworks** (Angular, Astro, Next.js completo)
- ✅ **Backend completo** (Express, Node, Strapi)

### Próximas Mejoras Planeadas
- AWS deployment & infrastructure
- Auth0 & JWT authentication
- Stripe payment integration
- Cloudinary image optimization
- SendGrid email services
- Microservices architecture
- GraphQL implementation
- API design patterns

---

## Notas por subcategoría

```dataview
TABLE title, subcategory, lastUpdated
FROM "01 Stack"
WHERE file.name != "_README"
SORT subcategory ASC, lastUpdated DESC
```

---

## Convención de esta carpeta

- **Una subcarpeta por tecnología** — si la nota no encaja en ninguna, probablemente va en `00 Fundamentos`
- **Máximo un nivel de profundidad** — no crear subcarpetas dentro de las subcarpetas
- **Nombres de archivo descriptivos** — `useState.md`, `App Router.md`, no `01-hook.md`
- **Links internos** — conectar notas relacionadas con `[[Nombre exacto]]`

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 4.0.0 | 2026-03-30 | OpenCode | Firebase, PostgreSQL, Redis, TypeScript, Testing, Docker (6 nuevos) |
| 3.0.0 | 2026-03-30 | Daniel | React Hooks expandido, Next.js profundizado, Tech-Services creado |
| 2.0.0 | 2026-03-30 | Daniel | Recuperación de 80+ archivos, reorganización, enriquecimiento |
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial (13 archivos) |
