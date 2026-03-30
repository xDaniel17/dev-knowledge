---
title: "04 Meta — Sistema y gestión del vault"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - meta
  - indice
  - plantillas
  - weekly-review
category: "04 Meta"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# 04 Meta

Esta carpeta contiene las herramientas para gestionar el vault: las plantillas y las revisiones semanales. Es el "sistema sobre el sistema".

---

## Plantillas disponibles

| Plantilla | Cuándo usarla | Link |
|-----------|--------------|------|
| **Template - Concepto** | Documentar un concepto teórico o principio (paradigmas, patrones de diseño, algoritmia) | [[Template - Concepto]] |
| **Template - Stack** | Documentar una tecnología, framework, librería o herramienta | [[Template - Stack]] |
| **Template - Flujo** | Documentar un proceso paso a paso que ejecutas en situaciones específicas | [[Template - Flujo]] |
| **Template - Referencia** | Capturar y procesar un recurso externo (artículo, video, herramienta) | [[Template - Referencia]] |
| **Template - Weekly Review** | Revisión semanal de aprendizaje y objetivos | [[Template - Weekly Review]] |

---

## Cómo usar las plantillas con Templater

1. Crea un nuevo archivo en la carpeta destino
2. En el editor, usa el comando: `Templater: Insert template`
3. Selecciona la plantilla correspondiente
4. Completa los campos del frontmatter
5. Escribe el contenido siguiendo las instrucciones de cada sección

---

## Crear el Weekly Review de esta semana

1. Determina el número de semana: `date +%Y-W%V` (en terminal)
2. Crea el archivo: `04 Meta/2026-W14.md` (ajusta el número)
3. Usa la plantilla **Template - Weekly Review**
4. Dedica 20-30 minutos a completarla

Las revisiones semanales están en la raíz de esta carpeta, no en subcarpetas, para facilitar su consulta cronológica.

---

## Revisiones semanales

```dataview
TABLE title, date
FROM "04 Meta"
WHERE contains(tags, "weekly-review")
SORT date DESC
```

---

## Link al README raíz

[[README]] — índice maestro del vault

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
