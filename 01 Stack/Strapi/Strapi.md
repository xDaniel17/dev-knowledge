---
title: Strapi
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["GraphQL", "Tecoloco-EcosistemaTrabajo", "Express", "Node.js", "TypeScript", "REST API"]
migrated: true
migratedFrom: 01 Stack/Strapi/Strapi.md
---

# Strapi: Headless CMS

Strapi es un **CMS (Content Management System) headless moderno y de código abierto** construido con Node.js. Permite crear, gestionar y entregar contenido a través de APIs REST y GraphQL, sin estar atado a una presentación específica en el frontend.

## 🎯 ¿Qué es Strapi?

Strapi es una solución **headless CMS** que separaa la gestión de contenido de su presentación. En lugar de generar HTML renderizado en el servidor (como WordPress tradicional), Strapi proporciona APIs potentes que tu aplicación frontend consume.

### Características principales

- **Headless CMS**: Gestión de contenido desacoplada de la presentación
- **API REST y GraphQL**: Múltiples formas de acceder al contenido
- **Admin Panel Intuitivo**: Interfaz web moderna y fácil de usar
- **Content Type Builder**: Define tipos de contenido sin escribir código
- **Draft & Publish**: Control de versiones de contenido
- **Internationalization (i18n)**: Soporte multiidioma nativo
- **Role-Based Access Control (RBAC)**: Control granular de permisos
- **Media Library**: Gestión integrada de archivos y medios
- **Customizable**: Totalmente extensible con plugins
- **Open Source**: Código abierto y comunidad activa
- **TypeScript Support**: Desarrollo moderno con seguridad de tipos
- **Deployment Flexible**: Deploya en cualquier plataforma

## 🛠️ Tech Stack

```
Node.js 18+ (requerido)
Strapi 5.x (última versión)
Base de datos:
  - SQLite (desarrollo)
  - PostgreSQL (producción)
  - MySQL (producción)
  - MariaDB (producción)
```

### Tech relacionadas

- [[Node.js]] - Motor de ejecución JavaScript
- [[Express]] - Framework web (Strapi lo usa internamente)
- [[REST API]] - API REST para acceder al contenido
- [[GraphQL]] - API GraphQL alternativa
- Database: PostgreSQL, MySQL, SQLite

## 📦 Instalación

### Requisitos previos

```bash
# Verificar versión de Node.js (18+ requerido)
node --version
npm --version
```

### Creación rápida de proyecto

```bash
# Instalación global de Strapi CLI (recomendado)
npm install -g @strapi/cli

# Crear nuevo proyecto con SQLite (desarrollo rápido)
strapi new my-project

# O especificar base de datos
strapi new my-project --db=postgres
strapi new my-project --db=mysql
strapi new my-project --db=mariadb
```

### Instalación manual

```bash
# Crear directorio del proyecto
mkdir my-strapi-project
cd my-strapi-project

# Inicializar npm
npm init -y

# Instalar Strapi
npm install @strapi/strapi

# Crear proyecto interactivamente
npx strapi new .
```

### Estructura del proyecto

```
my-strapi-project/
├── src/
│   ├── admin/              # Customizaciones del admin panel
│   ├── api/                # APIs (controllers, services, routes)
│   ├── plugins/            # Plugins personalizados
│   ├── middleware/         # Middlewares personalizados
│   ├── index.js           # Punto de entrada del servidor
│   └── config/            # Configuraciones globales
├── public/                 # Archivos estáticos
├── .env                   # Variables de entorno
├── .env.example          # Template de variables
├── package.json
└── package-lock.json
```

## 🚀 Primeros pasos

### Iniciar el servidor

```bash
# Modo desarrollo (con hot-reload)
npm run develop

# Acceder al Admin Panel
# http://localhost:1337/admin
```

### Crear cuenta administrador

En la primera ejecución, Strapi te pide crear una cuenta de administrador:
- Email
- Password
- Confirmar password

### Crear un Content Type

**Opción 1: Usando el Content-Type Builder (GUI)**

1. Ve a "Content-Type Builder" en el Admin Panel
2. Click en "Create new collection type"
3. Define el nombre (ej: "Article")
4. Agrega campos:
   - Title (Short text)
   - Description (Short text)
   - Content (Rich text)
   - Author (Text)
   - Published (Boolean)
5. Click "Save"

**Opción 2: Código (Estructura)**

```javascript
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
    "increments": true,
    "timestamps": true,
    "draftAndPublish": true
  },
  "attributes": {
    "title": {
      "type": "string",
      "required": true
    },
    "description": {
      "type": "string"
    },
    "content": {
      "type": "richtext"
    },
    "author": {
      "type": "string"
    },
    "publishedAt": {
      "type": "datetime"
    }
  }
}
```

### Agregar contenido

1. Ve a "Content Manager"
2. Selecciona tu Content Type (Article)
3. Click "Create new entry"
4. Completa los campos
5. Haz click en "Save" y luego "Publish" (si Draft & Publish está habilitado)

## 📊 Tipos de contenido y relaciones

### Tipos de campos disponibles

| Tipo | Descripción | Ejemplo |
|------|-------------|---------|
| **Text** | Texto corto | Título, nombre |
| **Rich Text** | Texto enriquecido | Artículos, descripciones largas |
| **Number** | Números enteros o decimales | Edad, precio |
| **Boolean** | Verdadero/Falso | Publicado, activo |
| **Date** | Fecha y/u hora | Fecha de publicación |
| **Email** | Campo de email | Email de contacto |
| **Media** | Archivos (imágenes, videos, documentos) | Portadas, attachments |
| **Single Type** | Contenido único | Configuración global |
| **Many-to-One** | Relación uno-a-muchos | Artículos de un autor |
| **One-to-One** | Relación uno-a-uno | Usuario ↔ Perfil |
| **Many-to-Many** | Relación muchos-a-muchos | Artículos ↔ Tags |

### Crear relaciones

```javascript
// Dentro de los atributos del schema
"author": {
  "type": "relation",
  "relation": "manyToOne",
  "target": "api::user.user",
  "inversedBy": "articles"
},
"categories": {
  "type": "relation",
  "relation": "manyToMany",
  "target": "api::category.category",
  "inversedBy": "articles"
}
```

## 🔐 Autenticación y autorización

### API Tokens (para acceso programático)

1. Ve al Admin Panel
2. Settings → API Tokens
3. "Create new API Token"
4. Define:
   - Name
   - Description
   - Type: Full access, Read-only
   - Expiration
5. Click "Save"

Usar el token en requests:

```bash
curl -H "Authorization: Bearer YOUR_API_TOKEN" \
  http://localhost:1337/api/articles
```

### Role-Based Access Control (RBAC)

1. Settings → Users & Permissions → Roles
2. Crear nuevo rol (ej: "Editor")
3. Definir permisos por Content Type:
   - Create
   - Read
   - Update
   - Delete
4. Asignar usuarios al rol

### Users & Permissions

```javascript
// Crear usuario programáticamente
POST /api/auth/local/register
{
  "username": "username",
  "email": "user@example.com",
  "password": "password123"
}
```

## 🌐 REST API

### Acceder a contenido

```bash
# Obtener todos los artículos
GET http://localhost:1337/api/articles

# Obtener un artículo específico
GET http://localhost:1337/api/articles/1

# Con paginación
GET http://localhost:1337/api/articles?pagination[pageSize]=10&pagination[page]=1

# Filtrar
GET http://localhost:1337/api/articles?filters[title][$contains]=JavaScript

# Ordenar
GET http://localhost:1337/api/articles?sort=createdAt:DESC

# Seleccionar campos específicos
GET http://localhost:1337/api/articles?fields[0]=title&fields[1]=author

# Poblar relaciones
GET http://localhost:1337/api/articles?populate=author,categories
```

### Crear, actualizar y eliminar

```bash
# Crear
POST /api/articles
Content-Type: application/json
Authorization: Bearer YOUR_TOKEN

{
  "data": {
    "title": "Mi Primer Artículo",
    "description": "Descripción del artículo",
    "author": "Juan",
    "publishedAt": "2026-03-29T10:00:00Z"
  }
}

# Actualizar
PUT /api/articles/1
Content-Type: application/json
Authorization: Bearer YOUR_TOKEN

{
  "data": {
    "title": "Título Actualizado"
  }
}

# Eliminar
DELETE /api/articles/1
Authorization: Bearer YOUR_TOKEN
```

### Respuesta de ejemplo

```json
{
  "data": {
    "id": 1,
    "attributes": {
      "title": "Mi Primer Artículo",
      "description": "Descripción del artículo",
      "author": "Juan",
      "createdAt": "2026-03-29T10:00:00.000Z",
      "updatedAt": "2026-03-29T10:00:00.000Z",
      "publishedAt": "2026-03-29T10:00:00.000Z"
    }
  },
  "meta": {}
}
```

## 📡 GraphQL API

### Habilitar GraphQL

```bash
npm install @strapi/plugin-graphql
```

Acceder a GraphQL Playground:
```
http://localhost:1337/graphql
```

### Queries

```graphql
# Obtener todos los artículos
query {
  articles {
    data {
      id
      attributes {
        title
        description
        author
        publishedAt
      }
    }
  }
}

# Obtener un artículo con relaciones
query {
  article(id: 1) {
    data {
      id
      attributes {
        title
        author {
          data {
            attributes {
              name
            }
          }
        }
        categories {
          data {
            attributes {
              name
            }
          }
        }
      }
    }
  }
}

# Con filtros y ordenamiento
query {
  articles(
    filters: { title: { contains: "JavaScript" } }
    sort: ["createdAt:desc"]
    pagination: { pageSize: 10 }
  ) {
    data {
      id
      attributes {
        title
      }
    }
  }
}
```

### Mutations

```graphql
# Crear un artículo
mutation {
  createArticle(data: {
    title: "Nuevo Artículo"
    description: "Descripción"
    author: "Juan"
  }) {
    data {
      id
      attributes {
        title
      }
    }
  }
}

# Actualizar un artículo
mutation {
  updateArticle(id: 1, data: {
    title: "Título Actualizado"
  }) {
    data {
      id
      attributes {
        title
      }
    }
  }
}

# Eliminar un artículo
mutation {
  deleteArticle(id: 1) {
    data {
      id
    }
  }
}
```

## 🔌 Plugins

### Plugins populares

| Plugin | Descripción |
|--------|-------------|
| **GraphQL** | API GraphQL para queries avanzadas |
| **Email** | Envío de emails transaccionales |
| **Upload** | Gestión mejorada de archivos |
| **i18n** | Internacionalización integrada |
| **Comments** | Sistema de comentarios |
| **Webhooks** | Disparadores de eventos |
| **Sitemap** | Generación automática de sitemap.xml |
| **SEO** | Optimización para motores de búsqueda |

### Instalar un plugin

```bash
npm install @strapi/plugin-name
npm run build
npm run develop
```

### Crear un plugin personalizado

```bash
# Generar estructura base del plugin
strapi generate plugin my-plugin

# Estructura creada
src/
├── plugins/
│   └── my-plugin/
│       ├── admin/
│       ├── server/
│       ├── package.json
│       └── strapi-server.js
```

## 🚢 Deployment

### Strapi Cloud (oficial)

Strapi proporciona hosting oficial en Strapi Cloud:

1. Ir a https://cloud.strapi.io
2. Conectar GitHub
3. Seleccionar repositorio
4. Configurar variables de entorno
5. Deploy automático en cada push

### Deployment en Vercel

```bash
# Crear archivo vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "build"
}
```

Conectar GitHub a Vercel y configurar:
- Install command: `npm install`
- Build command: `npm run build`
- Start command: `npm run start`
- Variables de entorno (`.env.production`)

### Deployment en Heroku

```bash
# Crear archivo Procfile
web: npm run start

# Crear archivo package.json scripts
"scripts": {
  "develop": "strapi develop",
  "start": "strapi start",
  "build": "strapi build",
  "strapi": "strapi"
}

# Deployar
git push heroku main
```

### Deployment en Docker

```dockerfile
# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 1337
CMD ["npm", "run", "start"]
```

```bash
# docker-compose.yml
version: '3.8'
services:
  strapi:
    build: .
    ports:
      - "1337:1337"
    environment:
      DATABASE_CLIENT: postgres
      DATABASE_HOST: postgres
      DATABASE_PORT: 5432
      DATABASE_NAME: strapi
      DATABASE_USERNAME: strapi
      DATABASE_PASSWORD: strapi
    depends_on:
      - postgres
  
  postgres:
    image: postgres:14
    environment:
      POSTGRES_DB: strapi
      POSTGRES_USER: strapi
      POSTGRES_PASSWORD: strapi
    volumes:
      - postgres_data:/var/lib/postgresql/data

volumes:
  postgres_data:
```

## 🔧 Configuración avanzada

### Variables de entorno

```bash
# .env
# Server
NODE_ENV=production
HOST=0.0.0.0
PORT=1337

# Database
DATABASE_CLIENT=postgres
DATABASE_HOST=db.example.com
DATABASE_PORT=5432
DATABASE_NAME=strapi_prod
DATABASE_USERNAME=strapi_user
DATABASE_PASSWORD=secure_password

# JWT
JWT_SECRET=your-secret-key

# API
API_TOKENS_ENABLED=true

# CORS
APP_KEYS=key1,key2,key3
API_TOKEN_SALT=token-salt
```

### Configurar CORS

```javascript
// config/middlewares.js
export default [
  'strapi::errors',
  'strapi::security',
  {
    name: 'strapi::cors',
    config: {
      enabled: true,
      origin: ['http://localhost:3000', 'https://example.com'],
      credentials: true,
    },
  },
  'strapi::poweredBy',
  'strapi::logger',
  'strapi::query',
  'strapi::body',
  'strapi::session',
  'strapi::favicon',
  'strapi::public',
];
```

### Lifecycle functions

```javascript
// src/api/article/controllers/article.js
export default {
  async beforeCreate(event) {
    const { data } = event.params;
    // Lógica antes de crear
    console.log('Creating article:', data);
  },

  async afterCreate(event) {
    const { result } = event.params;
    // Lógica después de crear
    console.log('Article created:', result);
  },

  async beforeUpdate(event) {
    const { data } = event.params;
    // Lógica antes de actualizar
  },

  async afterUpdate(event) {
    const { result } = event.params;
    // Lógica después de actualizar
  },
};
```

## 🧪 Testing

### Instalar dependencias de testing

```bash
npm install --save-dev jest @types/jest
```

### Ejemplo de test

```javascript
// tests/api/article.test.js
describe('Article API', () => {
  it('should retrieve articles', async () => {
    const response = await fetch('/api/articles');
    expect(response.status).toBe(200);
    const data = await response.json();
    expect(data.data).toBeDefined();
  });

  it('should create an article', async () => {
    const response = await fetch('/api/articles', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/json',
        'Authorization': `Bearer ${API_TOKEN}`,
      },
      body: JSON.stringify({
        data: {
          title: 'Test Article',
          description: 'Test description',
        },
      }),
    });
    expect(response.status).toBe(201);
  });
});
```

## 📱 Integraciones populares

### Con Next.js / React

```javascript
// fetch content from Strapi
const fetchArticles = async () => {
  const response = await fetch(
    'http://localhost:1337/api/articles?populate=*',
    {
      headers: {
        Authorization: `Bearer ${process.env.STRAPI_API_TOKEN}`,
      },
    }
  );
  return response.json();
};

export default async function Articles() {
  const { data } = await fetchArticles();
  return (
    <div>
      {data.map((article) => (
        <article key={article.id}>
          <h2>{article.attributes.title}</h2>
          <p>{article.attributes.description}</p>
        </article>
      ))}
    </div>
  );
}
```

### Con Vue.js

```javascript
// composables/useStrapi.js
import { ref } from 'vue';

export const useStrapi = () => {
  const articles = ref([]);

  const fetchArticles = async () => {
    const response = await fetch(
      'http://localhost:1337/api/articles?populate=*',
      {
        headers: {
          Authorization: `Bearer ${import.meta.env.VITE_STRAPI_TOKEN}`,
        },
      }
    );
    const { data } = await response.json();
    articles.value = data;
  };

  return { articles, fetchArticles };
};
```

### Con Nuxt.js

```javascript
// composables/useStrapi.ts
export const useStrapiArticles = () => {
  return useFetch('http://localhost:1337/api/articles', {
    headers: {
      Authorization: `Bearer ${useRuntimeConfig().public.strapiToken}`,
    },
  });
};
```

## 🐛 Troubleshooting

### Error: "Database connection failed"

```bash
# Verificar las variables de entorno
cat .env

# Verificar que la base de datos está corriendo
# Para PostgreSQL:
psql -h localhost -U username -d database_name

# Recrear la base de datos
npm run seed
```

### Admin panel no carga

```bash
# Limpiar caché y reconstruir
rm -rf .cache
rm -rf build
npm run build
npm run develop
```

### API devuelve error 403

- Verificar que el API Token es válido
- Verificar permisos en RBAC
- Verificar configuración de CORS

### Puerto 1337 ya en uso

```bash
# Cambiar puerto en .env
PORT=1338

# O matar el proceso
lsof -i :1337
kill -9 <PID>
```

## 🔗 Referencias relacionadas

- [[Node.js]] - Motor de ejecución
- [[Express]] - Framework web subyacente
- [[REST API]] - Patrón de arquitectura de API
- [[GraphQL]] - Lenguaje de query de datos
- [[TypeScript]] - Desarrollo con tipos
- Proyectos: [[Tecoloco-EcosistemaTrabajo]] (usa Strapi potencialmente)

## 📚 Recursos externos

- [Documentación oficial Strapi](https://docs.strapi.io)
- [Discord Community Strapi](https://discord.strapi.io)
- [Strapi Blog](https://strapi.io/blog)
- [Strapi Marketplace](https://market.strapi.io)
- [GitHub - Strapi](https://github.com/strapi/strapi)
- [Strapi Cloud](https://cloud.strapi.io)

## 🎓 Conceptos clave

**Headless CMS**: Sistema de gestión de contenido sin presentación predefinida. El contenido se accede a través de APIs.

**Content Type**: Estructura o esquema que define qué campos tiene una pieza de contenido (ej: Artículos, Productos).

**Populate**: Técnica para incluir relaciones anidadas en la respuesta de la API (similar a JOIN en SQL).

**Draft & Publish**: Flujo que permite crear borradores antes de publicar contenido.

**RBAC**: Role-Based Access Control - control de acceso basado en roles de usuario.

**Middleware**: Función que se ejecuta antes/después de ciertas operaciones en Strapi.

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documentación inicial completa: instalación, Content Types, REST API, GraphQL, autenticación, deployment, plugins, integraciones, troubleshooting |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
