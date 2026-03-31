---
title: Tech-Services — Plataformas y Servicios
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - services
  - platforms
  - hosting
  - databases
  - deployment
category: "01 Stack"
subcategory: "Tech-Services"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🔧 Tech-Services — Plataformas y Servicios

Documentación de servicios externos, plataformas de hosting, bases de datos y herramientas cloud.

---

## 🌐 Hosting y Deployment

### Vercel
[[Vercel.md]] - Plataforma oficial de Next.js
- Setup automático desde Git
- Preview URLs para PRs
- Edge Functions
- Analytics integrado
- **Mejor para:** Next.js y aplicaciones React

### Netlify
[[Netlify.md]] - Hosting flexible para JAMstack
- Static hosting + Serverless functions
- Forms integrado
- Split testing (A/B)
- Deploy desde Git
- **Mejor para:** Sitios estáticos, JAMstack

### Railway
[[Railway.md]] - Plataforma multilenguaje con databases integradas
- Deploy desde Git o Docker
- Bases de datos integradas (PostgreSQL, MongoDB, Redis)
- Pricing pay-as-you-go
- Soporte multilenguaje (Node, Python, Go, Rust, etc.)
- **Mejor para:** Backends, APIs, aplicaciones full-stack

---

## 🗄️ Bases de Datos

### MongoDB
[[MongoDB.md]] - Base de datos NoSQL
- Documentos JSON
- Setup local con mongosh
- Mongoose ORM
- Prisma support
- MongoDB Atlas (cloud)
- **Mejor para:** Aplicaciones JavaScript, flexible schema

### PostgreSQL & Supabase
[[PostgreSQL-Supabase.md]] - Base relacional robusta + Backend alternativo
- PostgreSQL instalación y configuración
- SQL avanzado y optimización
- Supabase (Firebase open-source)
- Autenticación integrada
- Real-time subscriptions
- **Mejor para:** Datos estructurados, relaciones complejas

### Firebase
[[Firebase.md]] - Backend as a Service completo
- Autenticación (Email, Google, GitHub)
- Firestore (NoSQL) y Real-time DB
- Cloud Storage para archivos
- Cloud Functions serverless
- Hosting integrado
- **Mejor para:** Prototipado rápido, MVPs

### Redis
[[Redis-Caching.md]] - Cache y base de datos en memoria
- Key-value store ultra-rápido
- Estrategias de caching (Cache-Aside, Write-Through)
- Sesiones y rate limiting
- Pub/Sub messaging
- Operaciones atómicas
- **Mejor para:** Caching, sesiones, real-time

---

## 📚 Próximas Adiciones

Planeado para futuros documentos:

- **AWS** - Infraestructura cloud completa
- **Google Cloud** - GCP services
- **Auth0** - Autenticación as a Service
- **Stripe** - Pagos online
- **Cloudinary** - CDN de imágenes
- **SendGrid** - Email marketing
- **Twilio** - SMS y comunicaciones

---

## 🔗 Enlaces Relacionados

- [[../Next.js/Deployment.md|Next.js Deployment]]
- [[../Node/Node.js.md|Node.js]]
- [[../Express/Express.js.md|Express]]
- [[../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.1.0 | 2026-03-30 | OpenCode | Agregado Firebase, PostgreSQL-Supabase, Redis |
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial con Vercel, Netlify, MongoDB |
