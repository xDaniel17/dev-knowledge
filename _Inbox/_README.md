---
title: "_Inbox — Captura rápida temporal"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - inbox
  - meta
  - proceso
category: "_Inbox"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# _Inbox

**Esta carpeta es temporal.** Aquí caen notas que aún no están procesadas: capturas rápidas, ideas a medias, cosas que leíste y quieres procesar después.

---

## Regla principal

> Una nota en Inbox por más de 2 semanas es **deuda técnica del vault**.

Si una nota lleva más de 2 semanas aquí, tienes tres opciones:
1. Procesarla y moverla a su carpeta definitiva
2. Descartarla (era menos útil de lo que creías)
3. Convertirla en una tarea explícita de aprendizaje

---

## Flujo de procesamiento (cada Weekly Review)

1. Lee cada nota en esta carpeta
2. Decide la carpeta destino: ¿es un concepto, una nota de stack, un flujo, una referencia?
3. Completa el frontmatter obligatorio (title, tags, category, subcategory)
4. Mueve el archivo a la carpeta correcta
5. Agrega links bidireccionales si aplica
6. Esta carpeta debe quedar vacía al finalizar la review

---

## Notas pendientes

```dataview
TABLE title, date, tags
FROM "_Inbox"
WHERE file.name != "_README"
SORT date ASC
```

---

## Cómo usar el Inbox

**Cuándo crear una nota aquí:**
- Acabas de leer algo interesante y no tienes tiempo de procesarlo ahora
- Tienes una idea a las 11pm que no quieres perder
- Encontraste un bug / solución que quieres documentar pero no tienes el contexto fresco

**Cuándo NO usar el Inbox:**
- Cuando ya sabes exactamente qué es la nota y dónde va — créala directamente en su carpeta
- Para notas que ya tienes completas

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
