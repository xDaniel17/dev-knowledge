---
title: "dev-knowledge — Índice maestro"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - meta
  - indice
  - readme
category: "Raíz"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# dev-knowledge

Base de conocimiento técnico personal o colectivo. Combina referencia rápida del stack de trabajo, fundamentos de ingeniería de software y herramientas de IA para desarrollo.

**Objetivo:** Que cada nota sea autocontenida y comprensible sin necesidad de leer el resto del vault. Útil tanto para revisión personal como como contexto para agentes IA.

---

## Estructura del vault

| Carpeta | Propósito | Índice |
|---------|-----------|--------|
| `00 Fundamentos/` | Teoría pura: paradigmas, principios, patrones, arquitecturas | [[00 Fundamentos/_README\|README]] |
| `01 Stack/` | Documentación técnica del stack: React, Next.js, Angular, Node, etc. | [[01 Stack/_README\|README]] |
| `02 Flujos/` | Procesos paso a paso: setups, deploys, configuraciones | [[02 Flujos/_README\|README]] |
| `03 Referencias/` | Recursos externos procesados: artículos, videos, herramientas | [[03 Referencias/_README\|README]] |
| `04 Meta/` | Plantillas y revisiones semanales | [[04 Meta/_README\|README]] |
| `_Inbox/` | Captura temporal — se procesa en la Weekly Review | [[_Inbox/_README\|README]] |
| `_Templates/` | Plantillas Templater para crear nuevas notas | — |
| `_Snippets/` | Código reutilizable por tecnología | [[_Snippets/_README\|README]] |

---

## Convenciones del sistema

### Nombres de archivo
- Descriptivos en español o inglés según convenga
- Sin números de prefijo: `useState.md` no `01-useState.md`
- Sin fechas en el nombre: la fecha va en el frontmatter
- CamelCase o espacios para nombres compuestos: `App Router.md`, `Event Loop.md`

### Frontmatter obligatorio
Todo archivo `.md` que no sea un snippet debe tener este frontmatter:

```yaml
---
title: "Título descriptivo"
date: YYYY-MM-DD
lastUpdated: YYYY-MM-DD
tags: [tag1, tag2]
category: "00 Fundamentos | 01 Stack | ..."
subcategory: "subcarpeta si aplica"
author: Daniel
version: 1.0.0
status: active
related: ["[[Nota relacionada]]"]
---
```

### Tags
- Minúsculas con guiones: `react-hooks`, `buenas-practicas`
- Sin espacios: `ia-agents` no `ia agents`
- Específicos y reutilizables: `nodejs` no `notas-de-node`

### Links internos
- Formato: `[[Nombre exacto del archivo]]`
- Solo linkear archivos que existen en el vault
- Preferir links descriptivos: `[[useState]]` no `ver aquí`

### Callouts
| Callout | Cuándo usarlo |
|---------|--------------|
| `> [!NOTE]` | Información adicional relevante |
| `> [!TIP]` | Consejo práctico o atajo |
| `> [!WARNING]` | Cuidado, error común o riesgo |
| `> [!IMPORTANT]` | Información crítica que no debe pasarse por alto |

---

## Cómo agregar una nota nueva

1. **Determina la carpeta:** ¿es teoría pura? → `00 Fundamentos`. ¿Es sobre una tecnología? → `01 Stack/<subcarpeta>`. ¿Es un proceso? → `02 Flujos`. ¿Es un recurso externo? → `03 Referencias`.

2. **Elige la plantilla:** En Obsidian, `Templater: Insert template` y selecciona la plantilla correspondiente.

3. **Completa el frontmatter** y escribe el contenido siguiendo las instrucciones de la plantilla.

4. **Agrega links** a notas relacionadas antes de cerrar.

---

## Cómo usar las plantillas con Templater

1. Crea el archivo en la carpeta destino
2. Abre el Command Palette (`Cmd/Ctrl + P`)
3. Busca y ejecuta: `Templater: Open Insert Template Modal`
4. Selecciona la plantilla apropiada
5. Templater insertará el contenido y puedes completar los campos

---

## Estado del vault

### Notas por carpeta

```dataview
TABLE length(rows) AS "Notas"
FROM ""
WHERE file.name != "_README" AND file.name != "README"
GROUP BY category
SORT rows DESC
```

### Últimas notas modificadas

```dataview
TABLE title, category, lastUpdated
FROM ""
WHERE file.name != "_README" AND file.name != "README"
SORT lastUpdated DESC
LIMIT 10
```

### Notas en Inbox (pendientes de procesar)

```dataview
TABLE title, date
FROM "_Inbox"
WHERE file.name != "_README"
SORT date ASC
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial del vault |
