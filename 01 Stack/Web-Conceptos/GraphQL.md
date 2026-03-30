---
title: GraphQL
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/Web-Conceptos/GraphQL.md
---

# 🔗 GraphQL

## ¿Qué es GraphQL?

**GraphQL** es un lenguaje de consulta y runtime de servidor para APIs. Permite a los clientes solicitar exactamente los datos que necesitan.

## REST vs GraphQL

| Aspecto | REST | GraphQL |
|---------|------|---------|
| **Datos** | Estructurado por endpoint | Solicitud flexible |
| **Múltiples recursos** | N+1 requests | Una sola query |
| **Documentación** | Manual | Autodocumentada |
| **Caché** | Nativa (HTTP) | Requiere configuración |
| **Curva de aprendizaje** | Baja | Media-Alta |

## Conceptos principales

### Query
Solicitar datos:
```graphql
query {
  usuario(id: 1) {
    id
    nombre
    email
    posts {
      id
      titulo
    }
  }
}
```

### Mutation
Modificar datos:
```graphql
mutation {
  crearUsuario(nombre: "Juan", email: "juan@email.com") {
    id
    nombre
    email
  }
}
```

### Subscription
Escuchar cambios en tiempo real:
```graphql
subscription {
  usuarioActualizado {
    id
    nombre
  }
}
```

## Schema

Define la estructura de datos y operaciones permitidas:

```graphql
type Usuario {
  id: ID!
  nombre: String!
  email: String!
  posts: [Post!]!
}

type Post {
  id: ID!
  titulo: String!
  contenido: String!
  autor: Usuario!
}

type Query {
  usuario(id: ID!): Usuario
  usuarios: [Usuario!]!
}

type Mutation {
  crearUsuario(nombre: String!, email: String!): Usuario
  actualizarUsuario(id: ID!, nombre: String): Usuario
}
```

## Ventajas

1. **Over-fetching** - Obtener solo lo necesario
2. **Under-fetching** - Una solicitud para múltiples recursos
3. **Documentación automática** - Schema autodocumentado
4. **Type safety** - Tipos definidos
5. **Developer experience** - Excelente tooling

## Desventajas

1. **Complejidad** - Más complejo que REST
2. **Caché** - Más difícil de cachear
3. **Archivos grandes** - Queries complejas pueden ser lentas
4. **Curva de aprendizaje** - Requiere entendimiento de GraphQL

## Servidores GraphQL populares

- **Apollo Server** - JavaScript/Node.js
- **GraphQL.js** - Referencia para JavaScript
- **Hasura** - GraphQL engine para PostgreSQL
- **PostGraphile** - GraphQL automático desde PostgreSQL

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documento inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
