---
title: MongoDB - Base de Datos NoSQL
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - mongodb
  - database
  - nosql
  - nodejs
  - orm
  - prisma
  - mongoose
category: "01 Stack"
subcategory: "Tech-Services"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🗄️ MongoDB - Base de Datos NoSQL

Base de datos basada en documentos JSON. Flexible, escalable y perfecta para aplicaciones JavaScript.

---

## 🎯 Conceptos Básicos

### Documento vs Tabla

```
SQL (PostgreSQL)      MongoDB
─────────────────────────────────
Table        →        Collection
Row          →        Document
Column       →        Field
```

### Ejemplo de Documento

```json
{
  "_id": ObjectId("507f1f77bcf86cd799439011"),
  "name": "Alice",
  "email": "alice@example.com",
  "posts": [
    { "id": 1, "title": "Primer post" }
  ],
  "createdAt": ISODate("2026-03-30")
}
```

---

## 🚀 Setup Local

### Installation

**macOS (Homebrew)**
```bash
brew tap mongodb/brew
brew install mongodb-community
brew services start mongodb-community

# Verificar
mongosh
```

**Windows (PowerShell Admin)**
```powershell
# Descargar desde mongodb.com/try/download/community

# O con Chocolatey:
choco install mongodb

# Iniciar servicio
net start MongoDB
```

**Linux (Ubuntu/Debian)**
```bash
curl https://www.mongodb.org/static/pgp/server-7.0.asc | sudo apt-key add -
echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu $(lsb_release -cs)/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
sudo apt-get update
sudo apt-get install -y mongodb-org
sudo systemctl start mongod
```

### MongoDB Atlas (Cloud)

```bash
# 1. Crear cuenta en mongodb.com/cloud
# 2. Create Cluster
# 3. Network Access → IP Address → 0.0.0.0/0 (desarrollo)
# 4. Crear Database User
# 5. Obtener connection string

mongodb+srv://user:password@cluster.mongodb.net/dbname?retryWrites=true&w=majority
```

---

## 💻 mongosh - Shell de MongoDB

### Comandos Básicos

**macOS/Linux:**
```bash
# Conectar local
mongosh

# Conectar a Atlas
mongosh "mongodb+srv://user:pass@cluster.mongodb.net/dbname"
```

**Windows (PowerShell):**
```powershell
# Conectar local
mongosh

# Conectar a Atlas
mongosh "mongodb+srv://user:pass@cluster.mongodb.net/dbname"
```

### Operaciones CRUD

```javascript
// Ver bases de datos
show databases

// Usar base de datos (crear si no existe)
use myapp

// Ver colecciones
show collections

// Insertar documento
db.users.insertOne({
  name: "Alice",
  email: "alice@example.com",
  age: 30
})

// Insertar múltiples
db.users.insertMany([
  { name: "Bob", email: "bob@example.com" },
  { name: "Charlie", email: "charlie@example.com" }
])

// Buscar
db.users.find()
db.users.find({ name: "Alice" })

// Actualizar
db.users.updateOne(
  { name: "Alice" },
  { $set: { age: 31 } }
)

// Eliminar
db.users.deleteOne({ name: "Alice" })

// Contar
db.users.countDocuments()

// Eliminar colección
db.users.drop()
```

---

## 🔗 ODM/ORM para Node.js

### Mongoose (Popular)

```bash
npm install mongoose
```

**Schema y Model:**
```typescript
import mongoose from 'mongoose';

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: true,
  },
  email: {
    type: String,
    unique: true,
    required: true,
  },
  age: Number,
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

export const User = mongoose.model('User', userSchema);
```

**Operaciones:**
```typescript
// Crear
const user = await User.create({
  name: 'Alice',
  email: 'alice@example.com',
  age: 30,
});

// Buscar uno
const user = await User.findById(id);
const user = await User.findOne({ email: 'alice@example.com' });

// Buscar múltiples
const users = await User.find({ age: { $gt: 25 } });

// Actualizar
await User.findByIdAndUpdate(id, { age: 31 });

// Eliminar
await User.findByIdAndDelete(id);

// Populate relaciones
const user = await User.findById(id).populate('posts');
```

### Prisma (Moderno)

```bash
npm install @prisma/client
npx prisma init
```

**schema.prisma:**
```prisma
datasource db {
  provider = "mongodb"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id    String  @id @default(auto()) @map("_id") @db.ObjectId
  name  String
  email String  @unique
  age   Int?
  posts Post[]
  createdAt DateTime @default(now())
}

model Post {
  id    String  @id @default(auto()) @map("_id") @db.ObjectId
  title String
  userId String @db.ObjectId
  user   User    @relation(fields: [userId], references: [id])
}
```

**Operaciones:**
```typescript
import { PrismaClient } from '@prisma/client';
const prisma = new PrismaClient();

// Crear
const user = await prisma.user.create({
  data: {
    name: 'Alice',
    email: 'alice@example.com',
  },
});

// Buscar
const user = await prisma.user.findUnique({ where: { id } });
const users = await prisma.user.findMany({ where: { age: { gt: 25 } } });

// Actualizar
await prisma.user.update({
  where: { id },
  data: { age: 31 },
});

// Eliminar
await prisma.user.delete({ where: { id } });

// Transacciones
await prisma.$transaction([
  prisma.user.update({ where: { id: 1 }, data: { age: 30 } }),
  prisma.user.update({ where: { id: 2 }, data: { age: 25 } }),
]);
```

---

## 🔍 Queries Avanzadas

### Operadores

```javascript
// Mayor que
{ age: { $gt: 25 } }

// Menor que
{ age: { $lt: 30 } }

// En array
{ status: { $in: ['active', 'pending'] } }

// Expresión regular
{ email: { $regex: '@example.com$' } }

// Exists
{ phone: { $exists: true } }

// Tipo
{ createdAt: { $type: 'date' } }
```

### Agregación

```javascript
db.users.aggregate([
  // Stage 1: Filtrar
  { $match: { age: { $gt: 25 } } },
  
  // Stage 2: Proyectar
  { $project: { name: 1, email: 1, age: 1 } },
  
  // Stage 3: Agrupar
  { $group: { 
    _id: null, 
    averageAge: { $avg: '$age' },
    count: { $sum: 1 }
  }},
  
  // Stage 4: Ordenar
  { $sort: { count: -1 } }
])
```

### Búsqueda de Texto

```javascript
// Crear índice de texto
db.posts.createIndex({ title: 'text', content: 'text' })

// Buscar
db.posts.find({ $text: { $search: 'mongodb nodejs' } })
```

---

## 🔐 Índices y Performance

### Crear Índices

```javascript
// Single field
db.users.createIndex({ email: 1 })

// Compound index
db.posts.createIndex({ userId: 1, createdAt: -1 })

// Unique index
db.users.createIndex({ email: 1 }, { unique: true })

// Text index
db.posts.createIndex({ title: 'text' })
```

### Ver Índices

```javascript
db.users.getIndexes()
db.users.stats()
```

### Eliminar Índice

```javascript
db.users.dropIndex('email_1')
```

---

## 🏗️ Patrón Típico: Next.js + MongoDB + Prisma

### 1. Setup

```bash
npm install @prisma/client
npx prisma init

# .env.local
DATABASE_URL="mongodb+srv://user:pass@cluster.mongodb.net/dbname"
```

### 2. Schema (prisma/schema.prisma)

```prisma
datasource db {
  provider = "mongodb"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model Post {
  id    String  @id @default(auto()) @map("_id") @db.ObjectId
  title String
  content String
  authorId String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

### 3. Server Action (app/actions.ts)

```typescript
'use server';

import { PrismaClient } from '@prisma/client';
import { revalidatePath } from 'next/cache';

const prisma = new PrismaClient();

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;

  const post = await prisma.post.create({
    data: { title, content, authorId: userId },
  });

  revalidatePath('/posts');
  return post;
}

export async function deletePost(id: string) {
  await prisma.post.delete({ where: { id } });
  revalidatePath('/posts');
}
```

### 4. Page (app/posts/page.tsx)

```typescript
import { PrismaClient } from '@prisma/client';
import { createPost, deletePost } from '@/app/actions';

const prisma = new PrismaClient();

async function getPosts() {
  return await prisma.post.findMany({
    orderBy: { createdAt: 'desc' },
  });
}

export default async function PostsPage() {
  const posts = await getPosts();

  return (
    <div>
      <form action={createPost}>
        <input name="title" placeholder="Título" />
        <textarea name="content" placeholder="Contenido" />
        <button type="submit">Crear Post</button>
      </form>

      <ul>
        {posts.map(post => (
          <li key={post.id}>
            <h3>{post.title}</h3>
            <p>{post.content}</p>
            <form action={async () => { 'use server'; await deletePost(post.id); }}>
              <button type="submit">Eliminar</button>
            </form>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## 🚀 MongoDB Atlas Tips

### Backups

```
Cluster → Backup
- Automated backups: cada 12 horas
- Manual snapshots: bajo demanda
- Restore to: 35 días atrás
```

### Monitoring

```
Cluster → Monitoring
- Memory usage
- Connections
- Throughput
- Disk space
```

### Scaling

```
Cluster → Configuration → Change Tier
- M0: Gratis (512MB)
- M2/M5: $9+/mes
- M10/M20: $57+/mes
```

---

## 💡 Best Practices

1. **Usar índices** para queries frecuentes
2. **Validar esquema** con mongoose/prisma
3. **Limitar documentos grandes** (< 16MB)
4. **Usar transactions** para operaciones críticas
5. **Monitorear performance** regularmente
6. **Backups automáticos** en producción
7. **Connection pooling** en applications

---

## 🔗 Enlaces Relacionados

- [[Vercel.md|Vercel]]
- [[Netlify.md|Netlify]]
- [[../Node/Node.js.md|Node.js]]
- [[../Express/Express.js.md|Express]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con setup, Mongoose, Prisma y ejemplos |
