---
title: Strapi - CMS Headless
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - strapi
  - cms
  - headless
  - api
  - backend
category: "01 Stack"
subcategory: "Strapi"
author: Daniel
version: 2.0.0
status: active
related: []
---

# Strapi

CMS headless Node.js. Genera APIs automáticamente desde definiciones de contenido.

---

## 📚 Documentación Completa

### Guía Principal
[[Strapi.md]] - Introducción a Strapi
- Qué es Strapi (headless CMS)
- Ventajas sobre CMS tradicionales
- Arquitectura
- Content Types
- API generada automáticamente

### Concepto Clave: Content Types
[[Content Types]] - Estructura de datos en Strapi
- Crear Content Types
- Fields y validaciones
- Relaciones
- Media
- Localizaciones

### Estructura de Proyecto
[[Estructura de proyecto.md]] - Organización del proyecto
- Carpetas y archivos
- Configuración
- Plugins
- Middlewares
- Lifecycle hooks

---

## 🔑 Conceptos Clave

### Content Type
```
Blog Post
├── title: String (required)
├── content: Rich Text
├── slug: String (unique)
├── author: Relation → User
├── tags: Tags
├── featured_image: Media
└── published_at: Date
```

### Auto-generated API
```bash
GET    /api/posts           # Get all
GET    /api/posts/:id       # Get one
POST   /api/posts           # Create
PUT    /api/posts/:id       # Update
DELETE /api/posts/:id       # Delete

# Con query params
GET /api/posts?populate=author,tags&filters[published]=true
```

### Lifecycle Hooks
```typescript
export default {
  async beforeCreate(event) {
    const { data } = event.params;
    // Validar, transformar datos
  },

  async afterCreate(event) {
    const { result } = event.params;
    // Ejecutar efectos secundarios
  }
};
```

---

## 📊 Arquitectura Típica

```
strapi-project/
├── src/
│   ├── api/
│   │   ├── post/               # Content Type
│   │   │   ├── content-types/post/schema.json
│   │   │   ├── controllers/
│   │   │   ├── routes/
│   │   │   └── services/
│   │   ├── user/
│   │   └── ...
│   ├── admin/
│   ├── extensions/
│   ├── middlewares/
│   ├── plugins/
│   └── index.js
├── strapi-admin.js
├── strapi-server.js
├── .env
└── package.json
```

---

## 🚀 Flujo Típico

1. **Definir Content Types** - En admin panel
2. **API generada automáticamente** - Endpoints RESTful
3. **Consumir desde frontend** - React, Next.js, etc.
4. **Customizaciones** - Controllers, services, hooks
5. **Plugins** - Extienda Strapi

---

## 🔗 Enlaces Relacionados

- [[../Express/Express.js.md|Express (Alternativa backend)]]
- [[../Node/Node.js.md|Node.js Runtime]]
- [[../Web-Conceptos/REST-API.md|REST API Design]]
- [[../../README|Mapa de Stack]]

---

## 💡 Ventajas de Headless

✓ **Separación de concernos** - CMS ≠ Frontend
✓ **APIs flexibles** - Consume desde múltiples clientes
✓ **Performance** - CDN-friendly
✓ **Escalabilidad** - Componentes independientes
✓ **Libertad de frontend** - Cualquier framework

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
