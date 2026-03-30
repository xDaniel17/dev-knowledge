---
title: Express.js - Backend Framework
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - express
  - nodejs
  - backend
  - api
  - middleware
category: "01 Stack"
subcategory: "Express"
author: Daniel
version: 2.0.0
status: active
related: []
---

# Express.js

Framework web minimalista para Node.js. Proporciona una capa robusta de características para web y aplicaciones móviles.

---

## 📚 Documentación Completa

### Guía Principal
[[Express.js.md]] - Introducción y conceptos fundamentales de Express
- Qué es Express
- Instalación y setup
- Routing básico
- Middlewares
- Request y Response objects
- Manejo de errores

### Creación de Proyectos
[[Creación de proyecto.md]] - Guía paso a paso para iniciar un proyecto Express
- Setup inicial
- Estructura recomendada
- Configuración básica
- Primeras rutas
- Ejemplo de API completa

### Core Features
[[Middlewares.md]] - Concepto y uso de middlewares
- Qué son los middlewares
- Orden de ejecución
- Middleware built-in
- Custom middlewares
- Error handling middlewares

---

## 🔑 Conceptos Clave

### Routing
```javascript
// GET, POST, PUT, DELETE, PATCH, etc.
app.get('/api/users/:id', (req, res) => {
  // Lógica de ruta
});
```

### Middlewares
```javascript
// Procesar requests antes de llegar a las rutas
app.use(express.json());
app.use(cors());
app.use(customMiddleware);
```

### Error Handling
```javascript
// Middleware de error (4 parámetros)
app.use((err, req, res, next) => {
  res.status(err.status || 500).json({ error: err.message });
});
```

---

## 📊 Arquitectura Típica

```
proyecto-express/
├── src/
│   ├── routes/         # Rutas de API
│   ├── controllers/    # Lógica de negocio
│   ├── middleware/     # Middlewares personalizados
│   ├── models/         # Esquemas de BD
│   ├── utils/          # Utilidades
│   └── app.js          # Configuración principal
├── .env                # Variables de entorno
├── .gitignore
├── package.json
└── README.md
```

---

## 🔗 Enlaces Relacionados

- [[../Node/Node.js.md|Node.js]]
- [[../Web-Conceptos/REST-API.md|REST API Design]]
- [[../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
