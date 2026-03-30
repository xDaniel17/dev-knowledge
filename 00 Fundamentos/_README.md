---
title: "00 Fundamentos — Índice"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - fundamentos
  - meta
  - indice
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# 00 Fundamentos

Esta carpeta contiene teoría pura y principios agnósticos al stack. El objetivo es que estas notas sean referencia duradera — los fundamentos cambian mucho más lento que los frameworks.

---

## Qué va aquí

- **Paradigmas de programación:** OOP, funcional, reactivo, declarativo vs imperativo
- **Principios de diseño:** SOLID, DRY, KISS, YAGNI, CUPID, Separation of Concerns
- **Patrones de diseño:**
  - Creacionales: Factory, Builder, Singleton, Prototype
  - Estructurales: Adapter, Decorator, Facade, Proxy
  - De comportamiento: Observer, Strategy, Command, Iterator, State
- **Arquitecturas:** MVC, Clean Architecture, Hexagonal (Ports & Adapters), Event-driven, Microservicios
- **Conceptos transversales:** Complejidad ciclomática, acoplamiento, cohesión, deuda técnica

---

## Notas en esta carpeta

```dataview
TABLE title, tags, lastUpdated
FROM "00 Fundamentos"
WHERE file.name != "_README"
SORT lastUpdated DESC
```

---

## Guía de uso

Las notas de Fundamentos suelen ser las más abstractas. Para que sean útiles a largo plazo:

1. Siempre incluye un ejemplo de código concreto que ilustre el concepto
2. Menciona cuándo **no** aplicar el principio — el dogmatismo es el enemigo
3. Conecta con notas de Stack que demuestren el concepto en un framework real

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
