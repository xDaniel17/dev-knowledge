---
title: Crear Proyecto Express
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [tecnologias, desarrollo]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/Express/Creación de proyecto.md
---

# Crear Proyecto Express

## 1. Inicializar proyecto

```bash
mkdir mi-api && cd mi-api
npm init -y
```

## 2. Instalar Express

```bash
npm install express
```

## 3. Estructura recomendada

```
mi-api/
├── src/
│   ├── index.js
│   ├── routes/
│   ├── controllers/
│   └── middlewares/
├── package.json
└── .env
```

## 4. Código base

```javascript
// src/index.js
const express = require('express')
const app = express()

app.use(express.json())

app.get('/health', (req, res) => {
  res.json({ status: 'ok' })
})

const PORT = process.env.PORT || 3000
app.listen(PORT, () => {
  console.log(`Server running on port ${PORT}`)
})
```

## 5. Scripts package.json

```json
{
  "scripts": {
    "start": "node src/index.js",
    "dev": "node --watch src/index.js"
  }
}
```

## 6. Ejecutar

```bash
npm run dev
```

## Mejores Prácticas

- Usar variables de entorno (.env)
- Separar rutas en archivos distintos
- Middleware para errores
- Validar request body

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
