---
title: "_Snippets — Biblioteca de código reutilizable"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - snippets
  - meta
  - codigo
category: "_Snippets"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# _Snippets

Esta carpeta contiene archivos de código puro organizados por tecnología. Son utilidades de referencia rápida — no notas con explicaciones, sino código listo para copiar y adaptar.

---

## Diferencia con las notas de Stack

| Notas de Stack | Snippets |
|----------------|---------|
| Explicación + código de ejemplo | Solo código |
| Frontmatter completo | Sin frontmatter de vault |
| Contexto y teoría | Implementación lista para usar |
| Una nota = un concepto | Un archivo = muchos snippets de una tecnología |

---

## Organización

Un archivo por tecnología o dominio:

```
_Snippets/
├── React-snippets.md       # Hooks, patrones, componentes comunes
├── TypeScript-snippets.md  # Types, generics, utility types
├── Bash-snippets.md        # Scripts, one-liners, automatización
├── SQL-snippets.md         # Queries frecuentes, patrones
├── CSS-snippets.md         # Animaciones, layouts, variables
└── Git-snippets.md         # Comandos compuestos, aliases
```

---

## Cómo referenciar desde notas

Desde cualquier nota del vault, puedes hacer link a un snippet:

```markdown
Para implementar la autenticación, consulta [[React-snippets]] sección "Auth Hook".
```

O copiar el snippet directamente en la nota cuando es específico de ese contexto.

---

## Criterio para agregar un snippet

Agrega un snippet cuando:
- Es código que escribes más de una vez
- Tiene una estructura no obvia que siempre tienes que buscar
- Es un patrón que te costó encontrar la primera vez

No agregues snippets de código trivial que cualquier autocomplete te daría.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
