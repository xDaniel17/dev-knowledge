---
title: Node.js - JavaScript Runtime
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nodejs
  - javascript
  - backend
  - runtime
  - event-loop
category: "01 Stack"
subcategory: "Node"
author: Daniel
version: 2.0.0
status: active
related: []
---

# Node.js

Entorno de ejecución para JavaScript en servidor. Event-driven, non-blocking I/O model.

---

## 📚 Documentación Completa

### Guía Principal
[[Node.js.md]] - Conceptos fundamentales de Node.js
- Qué es Node.js
- Event Loop (arquitectura central)
- Single-threaded model
- Non-blocking I/O
- Callbacks, Promises, async/await

### Instalación
- [[Instalación-Oficial.md]] - Instalación usando nodejs.org
- [[Instalación-fnm.md]] - Instalación con Fast Node Manager (recomendado)

### Guías Prácticas
[[Guías.md]] - Guías para tareas comunes
- Crear tu primer servidor
- npm y package.json
- Modules (CommonJS vs ESM)
- File system operations
- Debugging

---

## 🔑 Conceptos Clave

### Event Loop
El núcleo de Node.js:
1. **Call Stack** - Ejecuta código síncrono
2. **Task Queue** - Callbacks de I/O y timers
3. **Microtask Queue** - Promises y async/await
4. **Libuv** - Maneja operaciones asincrónicas

### Non-blocking I/O
```javascript
// Blocking (MAL)
const data = fs.readFileSync('archivo.txt');
console.log(data);

// Non-blocking (BIEN)
fs.readFile('archivo.txt', (err, data) => {
  console.log(data);
});
```

### Async/Await
```javascript
async function leer() {
  try {
    const data = await fs.promises.readFile('archivo.txt');
    console.log(data);
  } catch (err) {
    console.error(err);
  }
}
```

---

## 📊 Arquitectura Típica

```
proyecto-node/
├── src/
│   ├── server.js       # Punto de entrada
│   ├── routes/         # Rutas API
│   ├── controllers/    # Lógica
│   ├── models/         # BD
│   └── utils/          # Utilidades
├── tests/              # Tests
├── .env                # Env variables
├── .gitignore
├── package.json
└── README.md
```

---

## 🎯 Use Cases

- **APIs REST** - Express, Fastify
- **Real-time** - WebSockets, Socket.io
- **Microservicios** - Escalabilidad
- **CLI Tools** - Scripts de automatización
- **Build Tools** - Webpack, Vite, etc.

---

## 🔗 Enlaces Relacionados

- [[../Express/Express.js.md|Express Framework]]
- [[../Web-Conceptos/_README.md|Web Concepts]]
- [[../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
