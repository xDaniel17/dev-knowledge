---
title: "Middlewares en Express — Arquitectura y patrones"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - express
  - nodejs
  - middleware
  - backend
  - api
category: "01 Stack"
subcategory: "Express"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Event Loop]]"
---

# Middlewares en Express — Arquitectura y patrones

Un middleware en Express es una función con acceso al objeto `req`, `res`, y a la función `next()`. Son la pieza central de cómo Express procesa las peticiones HTTP.

---

## ¿Qué es un middleware?

```
Request → [MW1] → [MW2] → [MW3] → Route Handler → Response
              ↓ next()  ↓ next()  ↓ next()
```

Cada middleware puede:
1. Ejecutar cualquier código
2. Modificar `req` y `res`
3. Terminar el ciclo request-response
4. Llamar a `next()` para pasar al siguiente middleware

Si un middleware no llama `next()` ni envía una respuesta, la petición queda colgada.

---

## Firma de una función middleware

```typescript
import { Request, Response, NextFunction } from 'express';

// Middleware estándar
function myMiddleware(req: Request, res: Response, next: NextFunction) {
  // hacer algo con req / res
  next(); // pasar al siguiente
}

// Middleware de error — siempre 4 parámetros
function errorMiddleware(
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) {
  console.error(err.stack);
  res.status(500).json({ error: err.message });
}
```

> [!IMPORTANT]
> El middleware de error **siempre debe tener exactamente 4 parámetros**. Express lo detecta por la aridad de la función. Si le faltan parámetros, no será tratado como middleware de error.

---

## Orden de ejecución

El orden en que registras los middlewares importa. Express los ejecuta en el orden en que aparecen en el código:

```typescript
import express from 'express';

const app = express();

// 1. Se ejecuta primero — para todas las rutas
app.use(express.json());

// 2. Logger — para todas las rutas
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path}`);
  next();
});

// 3. Solo para rutas que empiecen con /api
app.use('/api', authMiddleware);

// 4. Handlers de rutas específicas
app.get('/api/users', getUsersHandler);

// 5. Ruta no encontrada — solo llega aquí si nadie respondió antes
app.use((req, res) => {
  res.status(404).json({ error: 'Not found' });
});

// 6. Error handler — siempre al FINAL
app.use(errorMiddleware);
```

---

## Ejemplos reales

### Auth JWT

```typescript
import jwt from 'jsonwebtoken';
import { Request, Response, NextFunction } from 'express';

// Extender el tipo Request para incluir el usuario
declare global {
  namespace Express {
    interface Request {
      user?: { id: string; role: string };
    }
  }
}

export function requireAuth(req: Request, res: Response, next: NextFunction) {
  const authHeader = req.headers.authorization;

  if (!authHeader?.startsWith('Bearer ')) {
    return res.status(401).json({ error: 'Token requerido' });
  }

  const token = authHeader.split(' ')[1];

  try {
    const payload = jwt.verify(token, process.env.JWT_SECRET!) as {
      id: string;
      role: string;
    };
    req.user = payload; // disponible en handlers posteriores
    next();
  } catch {
    res.status(401).json({ error: 'Token inválido o expirado' });
  }
}

// Uso
app.get('/api/profile', requireAuth, (req, res) => {
  res.json({ user: req.user });
});
```

### Logger estructurado

```typescript
export function requestLogger(req: Request, res: Response, next: NextFunction) {
  const start = Date.now();

  res.on('finish', () => {
    const duration = Date.now() - start;
    console.log(JSON.stringify({
      method: req.method,
      path: req.path,
      status: res.statusCode,
      duration: `${duration}ms`,
      ip: req.ip,
    }));
  });

  next();
}
```

### Rate limiting con express-rate-limit

```typescript
import rateLimit from 'express-rate-limit';

const apiLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutos
  max: 100, // máximo 100 requests por ventana por IP
  message: { error: 'Demasiadas peticiones, intenta más tarde' },
  standardHeaders: true, // incluye RateLimit-* headers
  legacyHeaders: false,
});

// Aplicar solo a rutas /api
app.use('/api', apiLimiter);
```

### CORS manual

```typescript
export function corsMiddleware(req: Request, res: Response, next: NextFunction) {
  const allowedOrigins = ['https://myapp.com', 'http://localhost:3000'];
  const origin = req.headers.origin;

  if (origin && allowedOrigins.includes(origin)) {
    res.setHeader('Access-Control-Allow-Origin', origin);
  }

  res.setHeader('Access-Control-Allow-Methods', 'GET,POST,PUT,PATCH,DELETE,OPTIONS');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');

  if (req.method === 'OPTIONS') {
    return res.status(204).end(); // preflight request
  }

  next();
}
```

---

## app.use() vs router.use()

```typescript
import { Router } from 'express';

const userRouter = Router();

// Middleware solo para las rutas de este router
userRouter.use(requireAuth);

userRouter.get('/', getAllUsers);
userRouter.post('/', createUser);
userRouter.get('/:id', getUserById);

// Montar el router en la app principal
app.use('/api/users', userRouter);
```

Usar routers permite organizar la aplicación por dominio y aplicar middlewares solo donde se necesitan.

---

## Organización en proyectos reales

```
src/
├── middlewares/
│   ├── auth.ts         # requireAuth, requireRole
│   ├── validate.ts     # validación con zod/joi
│   ├── logger.ts       # request logging
│   └── error.ts        # error handler global
├── routes/
│   ├── users.ts        # Router de usuarios
│   └── posts.ts        # Router de posts
└── app.ts              # setup de Express
```

```typescript
// app.ts
app.use(express.json());
app.use(requestLogger);
app.use(corsMiddleware);

app.use('/api/users', userRouter);
app.use('/api/posts', postRouter);

// Siempre al final
app.use(notFoundHandler);
app.use(errorHandler);
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
