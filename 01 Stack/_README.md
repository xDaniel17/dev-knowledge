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
| `React/` | Framework declarativo + Hooks completos | [[React/_README.md|useState, useEffect, useContext, useReducer, Patrones]] |
| `Next.js/` | React meta-framework (SSR, SSG, ISR) | [[Next.js/_README.md|App Router, Pages Router, Rendering strategies]] |
| `Angular/` | Framework enterprise TypeScript-first | [[Angular/_README.md|Signals, Directives, RxJS, Components]] |
| `Astro/` | Island architecture, SSG/SSR híbrido | [[Astro/_README.md|File-based routing, Zero JS by default]] |
| `CSS-Tailwind/` | Utility-first CSS framework | [[CSS-Tailwind/Utilidades clave.md|Responsive, Dark mode, Customización]] |

### Backend
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `Node/` | JavaScript runtime para servidor | [[Node/_README.md|Event Loop, Instalación, Guías]] |
| `Express/` | Minimal web framework | [[Express/_README.md|Middlewares, Routing, REST APIs]] |
| `Strapi/` | Headless CMS basado en Node | [[Strapi/_README.md|Content Types, Auto-generated APIs]] |

### Fundamentos Web
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `Web-Conceptos/` | Conceptos teóricos y arquitectónicos | [[Web-Conceptos/_README.md|REST API, GraphQL, Rendering Strategies, Arquitectura]] |

### IA & Automatización
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `IA-Agents/` | Agentes, MCP, Chain of Thought | [[IA-Agents/_README.md|Agentes-IA, MCP Protocol, Skills Ecosystem]] |
| `IA-Tools/` | Herramientas de IA para desarrollo | [[IA-Tools/_README.md|OpenCode, Claude Code, GitHub Copilot]] |
| `IA-Skills/` | Sistema de skills y extensiones | [[IA-Skills/_README.md|Skills, MCP Implementaciones (Anthropic, Vercel)]] |

### DevOps & Herramientas
| Subcarpeta | Descripción | Contenido |
|-----------|-------------|----------|
| `Git/` | Control de versiones distribuido | [[Git/_README.md|Comandos, Workflows, Git Flow, GitHub Flow]] |

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

- **Total de carpetas**: 13
- **Total de archivos**: 80+ (recuperados y mejorados)
- **READMEs por carpeta**: 11 (navegación mejorada)
- **Últimas actualizaciones**: 2026-03-30

---

## 🎯 Navegación Rápida

### Por Nivel
- **Principiantes**: [[React/_README.md|React Basics]], [[Web-Conceptos/_README.md|Web Conceptos]]
- **Intermedio**: [[Next.js/_README.md|Next.js]], [[Express/_README.md|Express]], [[Git/_README.md|Git]]
- **Avanzado**: [[Angular/_README.md|Angular]], [[IA-Agents/_README.md|IA Agents]], [[Node/_README.md|Node internals]]

### Por Caso de Uso
- **Frontend moderno**: React, Next.js, Angular, Astro
- **Backend robusto**: Node.js, Express, Strapi
- **IA y Automatización**: IA-Agents, IA-Tools, IA-Skills
- **Teoría Web**: Web-Conceptos, Rendering Strategies
- **DevOps**: Git, Workflows

---

## ✨ Mejoras Recientes

### Versión 2.0.0 (2026-03-30)
- ✅ **Recuperados 80+ archivos** de bóveda anterior
- ✅ **React Hooks completo** (useState, useEffect, useContext, useReducer, etc.)
- ✅ **Web Conceptos fundamentales** (REST, GraphQL, Arquitectura, Rendering)
- ✅ **IA-Agents enriquecido** (MCP, Agentes, Skills Ecosystem)
- ✅ **READMEs de navegación** en todas las carpetas principales
- ✅ **Documentación de frameworks** (Angular, Astro, Next.js completo)
- ✅ **Backend completo** (Express, Node, Strapi)
- ✅ **Git & DevOps** (Comandos, Workflows, Estrategias)

### Próximas Mejoras Planeadas
- CSS Frameworks Comparison
- Web Libraries (Redux, React-Query, Zustand)
- TypeScript Best Practices
- Performance Optimization Guides
- Testing Strategies (Jest, Testing Library, Cypress)
- Docker & Deployment Guides

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
| 2.0.0 | 2026-03-30 | Daniel | Recuperación de 80+ archivos, reorganización, enriquecimiento de documentación |
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial (13 archivos) |
