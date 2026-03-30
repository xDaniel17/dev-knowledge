---
title: "Estructura de proyecto Strapi v4/v5"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - strapi
  - cms
  - backend
  - api
  - nodejs
category: "01 Stack"
subcategory: "Strapi"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Middlewares]]"
  - "[[Event Loop]]"
---

# Estructura de proyecto Strapi v4/v5

Strapi es un CMS headless open-source. Genera automáticamente una API REST y GraphQL a partir de Content Types definidos en su UI o en código. Ideal para proyectos donde el cliente necesita gestionar contenido sin tocar código.

---

## Estructura de carpetas

```
my-strapi-project/
├── config/
│   ├── api.js          # Configuración de la API (rest, graphql)
│   ├── database.js     # Conexión a la base de datos
│   ├── middleware.js   # Middlewares globales
│   ├── plugins.js      # Configuración de plugins
│   └── server.js       # Puerto, host, cors
├── database/
│   └── migrations/     # Migraciones SQL (v5)
├── public/
│   └── uploads/        # Archivos subidos por Media Library
├── src/
│   ├── admin/          # Customización del panel admin
│   ├── api/            # Content Types y lógica de negocio
│   │   └── article/
│   │       ├── content-types/
│   │       │   └── article/
│   │       │       └── schema.json   # Definición del tipo
│   │       ├── controllers/
│   │       │   └── article.js        # Lógica de controllers
│   │       ├── routes/
│   │       │   └── article.js        # Definición de rutas
│   │       └── services/
│   │           └── article.js        # Lógica de negocio
│   ├── components/     # Componentes reutilizables (ej: SEO, Address)
│   ├── extensions/     # Extensiones de plugins existentes
│   └── index.js        # Bootstrap y register hooks
└── .env                # Variables de entorno
```

---

## Content Types: UI vs código

### Crear via UI (recomendado para empezar)

En el panel admin: Content-Type Builder → Create new collection type. Strapi genera automáticamente los archivos en `src/api/<nombre>/`.

### Schema en código

```json
// src/api/article/content-types/article/schema.json
{
  "kind": "collectionType",
  "collectionName": "articles",
  "info": {
    "singularName": "article",
    "pluralName": "articles",
    "displayName": "Article"
  },
  "options": {
    "draftAndPublish": true
  },
  "attributes": {
    "title": {
      "type": "string",
      "required": true,
      "maxLength": 255
    },
    "content": {
      "type": "richtext"
    },
    "slug": {
      "type": "uid",
      "targetField": "title"
    },
    "publishedAt": {
      "type": "datetime"
    },
    "author": {
      "type": "relation",
      "relation": "manyToOne",
      "target": "plugin::users-permissions.user"
    },
    "categories": {
      "type": "relation",
      "relation": "manyToMany",
      "target": "api::category.category"
    },
    "seo": {
      "type": "component",
      "repeatable": false,
      "component": "shared.seo"
    }
  }
}
```

---

## Lifecycle Hooks

Los lifecycle hooks permiten ejecutar lógica antes o después de operaciones del Content Manager:

```javascript
// src/api/article/content-types/article/lifecycles.js
module.exports = {
  async beforeCreate(event) {
    const { data } = event.params;
    // Generar slug automáticamente si no viene
    if (!data.slug && data.title) {
      data.slug = data.title
        .toLowerCase()
        .replace(/\s+/g, '-')
        .replace(/[^a-z0-9-]/g, '');
    }
  },

  async afterCreate(event) {
    const { result } = event;
    // Notificar a un webhook externo
    await fetch('https://hooks.example.com/new-article', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ id: result.id, title: result.title }),
    });
  },

  async beforeUpdate(event) {
    event.params.data.lastModified = new Date().toISOString();
  },
};
```

---

## REST API: populate y filtros

Strapi no devuelve relaciones por defecto. Hay que pedirlas explícitamente con `populate`:

```bash
# Obtener artículos con su autor y categorías
GET /api/articles?populate=author,categories

# Populate profundo (relaciones de relaciones)
GET /api/articles?populate[author][populate]=avatar&populate[categories]=*

# Populate todos los campos (usar con cuidado en producción)
GET /api/articles?populate=*
```

**Filtros:**

```bash
# Filtrar por campo
GET /api/articles?filters[title][$contains]=React

# Filtrar por relación
GET /api/articles?filters[author][username][$eq]=daniel

# Múltiples filtros con operadores
GET /api/articles?filters[$and][0][publishedAt][$notNull]=true&filters[$and][1][categories][name][$eq]=JavaScript

# Paginación
GET /api/articles?pagination[page]=1&pagination[pageSize]=10

# Ordenar
GET /api/articles?sort=publishedAt:desc
```

---

## Controladores personalizados

```javascript
// src/api/article/controllers/article.js
'use strict';

const { createCoreController } = require('@strapi/strapi').factories;

module.exports = createCoreController('api::article.article', ({ strapi }) => ({
  // Extender el findOne para incluir vistas
  async findOne(ctx) {
    const response = await super.findOne(ctx);

    // Incrementar contador de vistas
    if (response.data) {
      await strapi.entityService.update('api::article.article', response.data.id, {
        data: { views: (response.data.attributes.views || 0) + 1 },
      });
    }

    return response;
  },

  // Acción personalizada
  async featured(ctx) {
    const articles = await strapi.entityService.findMany('api::article.article', {
      filters: { featured: true },
      populate: ['author', 'categories'],
      sort: { publishedAt: 'desc' },
      limit: 5,
    });

    return this.transformResponse(articles);
  },
}));
```

```javascript
// src/api/article/routes/article.js
'use strict';

const { createCoreRouter } = require('@strapi/strapi').factories;

module.exports = createCoreRouter('api::article.article', {
  config: {
    // Añadir ruta personalizada
    custom: [
      {
        method: 'GET',
        path: '/articles/featured',
        handler: 'article.featured',
        config: { policies: [], middlewares: [] },
      },
    ],
  },
});
```

---

## Autenticación con API Token

Para acceso programático (desde un frontend o agente):

1. Admin panel → Settings → API Tokens → Create new API Token
2. Tipo: `Full access` o `Custom` con permisos específicos
3. Usar el token en las peticiones:

```bash
curl -H "Authorization: Bearer <tu-api-token>" \
  https://tu-strapi.com/api/articles
```

```typescript
// En Next.js o cualquier frontend
const response = await fetch(`${process.env.STRAPI_URL}/api/articles?populate=*`, {
  headers: {
    Authorization: `Bearer ${process.env.STRAPI_API_TOKEN}`,
  },
  next: { revalidate: 60 },
});
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
