---
title: Arquitectura Web
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/Web-Conceptos/Arquitectura-Web.md
---

# 🏗️ Arquitectura Web

## ¿Qué es la arquitectura web?

La arquitectura web define cómo se estructuran, comunican e interactúan los componentes de una aplicación web.

## Componentes principales

### Client (Navegador)
- **HTML** - Estructura
- **CSS** - Estilos
- **JavaScript** - Lógica interactiva
- **Almacenamiento local** - LocalStorage, SessionStorage

### Server (Backend)
- **Aplicación** - Node.js, Python, Java, etc.
- **Base de datos** - PostgreSQL, MongoDB, etc.
- **APIs** - REST, GraphQL
- **Autenticación** - JWT, OAuth

### Red
- **HTTP/HTTPS** - Protocolo de comunicación
- **DNS** - Resolución de dominios
- **CDN** - Distribución de contenido

## Modelos arquitectónicos

### Monolítica
- Backend y frontend juntos
- Ventaja: Simple de comenzar
- Desventaja: Difícil de escalar

### Microservicios
- Múltiples servicios pequeños independientes
- Ventaja: Escalabilidad y mantenibilidad
- Desventaja: Complejidad

### Serverless
- Sin gestión de servidores
- Ventaja: Bajo costo, escalabilidad automática
- Desventaja: Limitaciones de ejecución

## Rendering

### Server-Side Rendering (SSR)
- HTML se genera en el servidor
- Ejemplo: Next.js, Astro

### Client-Side Rendering (CSR)
- HTML se genera en el navegador
- Ejemplo: React SPA

### Static Site Generation (SSG)
- HTML se pre-genera en tiempo de build
- Ejemplo: Gatsby, Astro

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documento inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
