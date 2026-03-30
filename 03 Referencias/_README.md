---
title: "03 Referencias — Recursos externos procesados"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - referencias
  - meta
  - indice
  - recursos
category: "03 Referencias"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# 03 Referencias

Esta carpeta captura recursos externos (artículos, videos, herramientas, librerías) que encontraste valioso. No es un bookmark — es una versión procesada y resumida que puedes consultar sin volver al recurso original.

---

## Filosofía

Guardar un link es fácil. Procesar lo que aprendiste de él es lo que construye conocimiento. Cada nota en esta carpeta debe responder: **¿qué cambié en mi forma de programar gracias a este recurso?**

---

## Convención de tags por tipo

| Tag | Tipo de recurso |
|-----|----------------|
| `article` | Artículo de blog o publicación |
| `video` | Video de YouTube, conferencia, curso |
| `tool` | Herramienta, CLI, extensión |
| `library` | Librería o paquete npm |
| `doc` | Documentación oficial |
| `book` | Libro técnico o capítulo |
| `podcast` | Episodio de podcast |

---

## Todas las referencias

```dataview
TABLE title, type, source, date
FROM "03 Referencias"
WHERE file.name != "_README"
SORT date DESC
```

---

## Por tipo de recurso

```dataview
TABLE title, date
FROM "03 Referencias"
WHERE file.name != "_README"
GROUP BY type
SORT date DESC
```

---

## Cuándo crear una referencia

Crea una nota de referencia cuando:
- Leíste/viste algo que cambió cómo haces algo
- Es un recurso al que probablemente volverás
- Tiene información técnica densa que querrás tener resumida

No crees una referencia para:
- Recursos que solo ojeaste
- Documentación oficial que puedes consultar directamente
- Artículos que no aportaron nada nuevo

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
