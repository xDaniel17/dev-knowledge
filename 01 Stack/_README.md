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

| Subcarpeta | Descripción |
|-----------|-------------|
| `React/` | Hooks, componentes, patrones de estado y render |
| `Next.js/` | App Router, Pages Router, SSR, ISR, Server Actions |
| `Angular/` | Signals, componentes standalone, RxJS, inyección de dependencias |
| `Astro/` | Island architecture, content collections, SSG/SSR |
| `Node/` | Event Loop, streams, worker threads, módulos nativos |
| `Express/` | Middlewares, routing, error handling, arquitectura REST |
| `Strapi/` | Content Types, API, lifecycle hooks, plugins |
| `CSS-Tailwind/` | Utilidades, responsive, dark mode, customización |
| `Git/` | Workflows, comandos, resolución de conflictos |
| `IA-Agents/` | Prompting, chain of thought, system prompts, arquitecturas de agentes |
| `IA-Tools/` | Herramientas de IA para desarrollo: OpenCode, Claude Code, Cursor |
| `IA-Skills/` | Skills de Claude Code: creación, estructura, ejemplos |

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
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
