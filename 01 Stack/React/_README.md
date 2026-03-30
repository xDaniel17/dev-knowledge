---
title: React — Hooks, Componentes y Patrones
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - react
  - hooks
  - patrones
  - frontend
category: "01 Stack"
subcategory: "React"
author: Daniel
version: 2.0.0
status: active
related: []
---

# React

Documentación técnica integral de React: hooks, componentes, patrones de estado y rendering.

---

## 🪝 Hooks (Manejo de Estado)

| Hook | Descripción | Archivo |
|------|-------------|---------|
| **useState** | Agregar estado local a componentes | [[useState.md]] |
| **useReducer** | Estado complejo con lógica centralizada | [[useReducer.md]] |
| **useEffect** | Efectos secundarios y ciclo de vida | [[useEffect.md]] |
| **useContext** | Compartir datos sin prop drilling | [[useContext.md]] |
| **useMemo** | Memoizar cálculos costosos | [[useMemo.md]] |
| **useCallback** | Memoizar funciones callback | [[useCallback.md]] |
| **useRef** | Acceso directo al DOM y valores mutables | [[useRef.md]] |
| **useId** | Generar IDs únicos | [[useId.md]] |
| **useLayoutEffect** | Efectos síncronos con DOM | [[useLayoutEffect.md]] |

---

## 🔧 Custom Hooks

Crear hooks reutilizables y modulares:
- [[useCustomHooks.md]] - Patrones para custom hooks
- [[useFetch.md]] - Hook para data fetching
- [[useLocalStorage.md]] - Hook para localStorage

---

## 🎨 Componentes y Patrones

| Patrón | Descripción | Archivo |
|--------|-------------|---------|
| **Componentes** | Patrones de diseño de componentes | [[Componentes.md]] |
| **Fetch** | Patrones de obtención de datos | [[Fetch.md]] |
| **Buenas Prácticas** | Mejores prácticas en React | [[BuenasPracticas.md]] |

---

## 🚀 Guías de Inicio

- [[CreacionProyectos.md]] - Crear proyectos con React
- [[Vite.md]] - React con Vite
- [[NextJS.md]] - React + Next.js (SSR/SSG)

---

## 📚 Concepto General

React es una librería de JavaScript para construir interfaces de usuario declarativas con componentes reutilizables. Los **hooks** (disponibles desde React 16.8) permiten:

- Usar estado en componentes funcionales
- Reutilizar lógica de estado entre componentes
- Gestionar efectos secundarios de forma organizada

### Principios Clave

1. **Hooks solamente en componentes** — No en loops, condicionales o funciones anidadas
2. **Orden importa** — React depende del orden de los hooks
3. **Componentes puros** — Evitar mutaciones directas de estado
4. **Performance** — Usar useMemo y useCallback para optimizar

---

## 🔗 Enlaces Relacionados

- [[../../README|Mapa de Stack]]
- [[../Next.js/App Router.md|Next.js]]
- [[../../00 Fundamentos/README|Fundamentos Web]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
