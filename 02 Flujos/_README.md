---
title: "02 Flujos — Procesos operativos"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - flujos
  - meta
  - indice
  - proceso
category: "02 Flujos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related: []
---

# 02 Flujos

Esta carpeta contiene procesos paso a paso que ejecutas en situaciones específicas. A diferencia de las notas de Stack (que explican cómo funciona una tecnología), los Flujos explican **cómo hacer algo concreto** de principio a fin.

---

## Diferencia entre un Flujo y una nota de Stack

| Nota de Stack | Flujo |
|---------------|-------|
| ¿Qué es y cómo funciona? | ¿Cómo lo hago paso a paso? |
| Referencia permanente | Proceso que puede cambiar |
| Agnóstico de contexto | Específico de mi entorno |
| Conceptual + ejemplos | Comandos ejecutables |
| Ej: `useState.md` | Ej: `Setup Proyecto Next.md` |

---

## Flujos disponibles

### Setup-Environment/

Flujos para configurar entornos de desarrollo:

| Flujo | Trigger |
|-------|---------|
| *(pendiente)* Setup proyecto Next.js + Strapi | Nuevo proyecto fullstack |
| *(pendiente)* Configurar VS Code para TypeScript | Nuevo equipo o reinstalación |

### Deployment/

Flujos para desplegar aplicaciones:

| Flujo | Trigger |
|-------|---------|
| *(pendiente)* Deploy a Vercel con variables de entorno | Primera vez en Vercel |
| *(pendiente)* Pipeline CI/CD con GitHub Actions | Setup de automatización |

---

## Notas en esta carpeta

```dataview
TABLE title, subcategory, lastUpdated
FROM "02 Flujos"
WHERE file.name != "_README"
SORT subcategory ASC, lastUpdated DESC
```

---

## Cómo mantener los flujos actualizados

Los flujos se desactualizan. Cada vez que ejecutas un flujo y algo no funciona como se describe, actualiza la nota inmediatamente. Un flujo desactualizado es peor que no tener flujo.

Marcas que indican que un flujo necesita revisión:
- Un paso falló con un error diferente al documentado
- Cambió la versión de una herramienta principal
- Encontraste un atajo que reduce pasos

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
