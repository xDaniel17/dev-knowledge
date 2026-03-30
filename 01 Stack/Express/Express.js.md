---
title: Express.js
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [tecnologias, desarrollo]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["01 Tecnolog\u00edas/Web/Next.js/Gu\u00edas/Creaci\u00f3n de proyecto/Creaci\u00f3n de proyecto"]
migrated: true
migratedFrom: 01 Stack/Express/Express.js.md
---

# Express.js

Framework web minimalista para Node.js.

- **Docs**: [expressjs.com](https://expressjs.com)
- **npm**: [npmjs.com/package/express](https://npmjs.com/package/express)

## Instalación

```bash
npm install express
```

## Servidor Básico

```javascript
const express = require('express')
const app = express()

app.get('/', (req, res) => {
  res.send('Hola mundo')
})

app.listen(3000)
```

## Routing

```javascript
// GET
app.get('/users', (req, res) => {
  res.json({ users: [] })
})

// POST
app.post('/users', (req, res) => {
  res.status(201).json({ id: 1 })
})

// PUT
app.put('/users/:id', (req, res) => {
  res.json({ updated: true })
})

// DELETE
app.delete('/users/:id', (req, res) => {
  res.status(204).send()
})
```

## Middleware

```javascript
// Body parser
app.use(express.json())
app.use(express.urlencoded({ extended: true }))

// Logger
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path}`)
  next()
})

// Error handler
app.use((err, req, res, next) => {
  console.error(err.stack)
  res.status(500).json({ error: 'Error' })
})
```

## Parámetros

```javascript
// Route params
app.get('/users/:id', (req, res) => {
  const { id } = req.params
})

// Query params
app.get('/search', (req, res) => {
  const { q, page } = req.query
})
```

## Respuestas

| Método | Descripción |
|--------|-------------|
| `res.send()` | Enviar texto/JSON |
| `res.json()` | Enviar JSON |
| `res.status()` | Establecer código |
| `res.redirect()` | Redireccionar |
| `res.sendFile()` | Enviar archivo |

## Guías

- [[01 Tecnologías/Web/Next.js/Guías/Creación de proyecto/Creación de proyecto|Crear proyecto]]

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
