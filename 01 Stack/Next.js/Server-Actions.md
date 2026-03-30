---
title: Server Actions en Next.js
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nextjs
  - server-actions
  - forms
  - mutations
  - rsc
category: "01 Stack"
subcategory: "Next.js"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🔨 Server Actions en Next.js 14+

Ejecutar código del servidor directamente desde formularios y acciones del cliente sin crear API routes.

---

## 🎯 ¿Qué son Server Actions?

Server Actions son **funciones asincrónicas que se ejecutan en el servidor**. Se definen con la directiva `'use server'` y pueden ser invocadas desde Client Components mediante:

1. **Atributo `action`** en formularios
2. **Llamadas directorias** con `useTransition`
3. **Event handlers**

---

## 📝 Sintaxis Básica

### Opción 1: En un módulo de Server Actions

```tsx
// app/actions.ts
'use server';

export async function createPost(formData: FormData) {
  const title = formData.get('title') as string;
  const content = formData.get('content') as string;

  // Validar
  if (!title || title.length < 3) {
    throw new Error('Título muy corto');
  }

  // Crear en BD
  const post = await db.post.create({
    data: { title, content },
  });

  // Revalidar caché
  revalidatePath('/posts');

  return post;
}
```

### Opción 2: Inline en Client Component

```tsx
'use client';

export default function CreatePostForm() {
  async function handleSubmit(formData: FormData) {
    'use server'; // Marca como Server Action

    const title = formData.get('title') as string;
    await db.post.create({ data: { title } });
    revalidatePath('/posts');
  }

  return (
    <form action={handleSubmit}>
      <input name="title" />
      <button type="submit">Crear</button>
    </form>
  );
}
```

---

## 🎭 Patrones Comunes

### Pattern 1: Form Submission Simple

```tsx
// app/actions.ts
'use server';

import { revalidatePath } from 'next/cache';

export async function addTodo(formData: FormData) {
  const title = formData.get('title')?.toString();

  if (!title) throw new Error('Title is required');

  await db.todo.create({ data: { title } });
  revalidatePath('/todos');
}
```

```tsx
// app/todos/page.tsx
import { addTodo } from '@/app/actions';

export default function TodosPage() {
  return (
    <>
      <form action={addTodo}>
        <input name="title" placeholder="Nueva tarea" />
        <button type="submit">Agregar</button>
      </form>
    </>
  );
}
```

### Pattern 2: Con Loading y Errores

```tsx
'use client';

import { useTransition } from 'react';
import { updateProfile } from '@/app/actions';

export function ProfileForm({ userId }: { userId: string }) {
  const [isPending, startTransition] = useTransition();
  const [error, setError] = useState<string | null>(null);

  function handleSubmit(formData: FormData) {
    setError(null);
    startTransition(async () => {
      try {
        await updateProfile(userId, formData);
      } catch (err) {
        setError(err.message);
      }
    });
  }

  return (
    <form action={handleSubmit}>
      <input name="email" />
      <button disabled={isPending} type="submit">
        {isPending ? 'Guardando...' : 'Guardar'}
      </button>
      {error && <p className="error">{error}</p>}
    </form>
  );
}
```

### Pattern 3: Con Validación y Respuesta

```tsx
// app/actions.ts
'use server';

import { z } from 'zod';

const schema = z.object({
  email: z.string().email(),
  name: z.string().min(2),
});

export async function updateProfile(
  userId: string,
  formData: FormData
) {
  const data = {
    email: formData.get('email'),
    name: formData.get('name'),
  };

  // Validar con Zod
  const result = schema.safeParse(data);
  if (!result.success) {
    throw new Error(result.error.issues[0].message);
  }

  // Actualizar
  const updated = await db.user.update({
    where: { id: userId },
    data: result.data,
  });

  revalidatePath(`/users/${userId}`);

  return { success: true, user: updated };
}
```

```tsx
// app/profile/page.tsx
'use client';

import { useState } from 'react';
import { updateProfile } from '@/app/actions';

export default function ProfilePage({ userId }: { userId: string }) {
  const [message, setMessage] = useState('');

  async function handleSubmit(formData: FormData) {
    try {
      const result = await updateProfile(userId, formData);
      setMessage('Perfil actualizado!');
    } catch (error) {
      setMessage(`Error: ${error.message}`);
    }
  }

  return (
    <form action={handleSubmit}>
      <input name="email" type="email" />
      <input name="name" />
      <button type="submit">Guardar</button>
      {message && <p>{message}</p>}
    </form>
  );
}
```

### Pattern 4: Múltiples Acciones

```tsx
'use client';

import { useState } from 'react';
import { deletePost, publishPost, updatePost } from '@/app/actions';

export function PostActions({ postId }: { postId: string }) {
  const [status, setStatus] = useState('idle');

  const handlePublish = async () => {
    setStatus('publishing');
    await publishPost(postId);
    setStatus('idle');
  };

  const handleDelete = async () => {
    if (!confirm('¿Seguro?')) return;
    setStatus('deleting');
    await deletePost(postId);
    setStatus('deleted');
  };

  return (
    <div>
      <button onClick={handlePublish} disabled={status !== 'idle'}>
        Publicar
      </button>
      <button onClick={handleDelete} disabled={status !== 'idle'}>
        Eliminar
      </button>
      {status !== 'idle' && <p>{status}...</p>}
    </div>
  );
}
```

### Pattern 5: Validación en Tiempo Real

```tsx
// app/actions.ts
'use server';

export async function validateEmail(email: string) {
  // Verificar si ya existe
  const exists = await db.user.findUnique({
    where: { email },
  });

  return {
    available: !exists,
    message: exists ? 'Email ya registrado' : 'Email disponible',
  };
}
```

```tsx
'use client';

import { useOptimistic, useState } from 'react';
import { validateEmail } from '@/app/actions';

export function EmailInput() {
  const [email, setEmail] = useState('');
  const [validation, setValidation] = useState(null);

  const handleChange = async (e: React.ChangeEvent<HTMLInputElement>) => {
    const value = e.target.value;
    setEmail(value);

    if (value.includes('@')) {
      const result = await validateEmail(value);
      setValidation(result);
    }
  };

  return (
    <div>
      <input
        type="email"
        value={email}
        onChange={handleChange}
        placeholder="tu@email.com"
      />
      {validation && (
        <p style={{ color: validation.available ? 'green' : 'red' }}>
          {validation.message}
        </p>
      )}
    </div>
  );
}
```

---

## 🔐 Seguridad

### Autenticación

```tsx
// app/actions.ts
'use server';

import { auth } from '@/auth';

export async function createPost(formData: FormData) {
  const session = await auth();

  if (!session?.user) {
    throw new Error('No autorizado');
  }

  // Usar session.user.id de forma segura
  const post = await db.post.create({
    data: {
      title: formData.get('title') as string,
      authorId: session.user.id,
    },
  });

  return post;
}
```

### Autorización

```tsx
// app/actions.ts
'use server';

export async function deletePost(postId: string) {
  const session = await auth();

  if (!session?.user) {
    throw new Error('No autorizado');
  }

  // Verificar que el usuario es propietario
  const post = await db.post.findUnique({ where: { id: postId } });

  if (post?.authorId !== session.user.id) {
    throw new Error('No tienes permiso para eliminar este post');
  }

  await db.post.delete({ where: { id: postId } });
  revalidatePath('/posts');
}
```

---

## 🧠 useOptimistic

Actualizar UI de forma optimista mientras el servidor procesa:

```tsx
'use client';

import { useOptimistic } from 'react';
import { addTodo } from '@/app/actions';

export function TodoList({ initialTodos }: { initialTodos: Todo[] }) {
  const [optimisticTodos, addOptimisticTodo] = useOptimistic(
    initialTodos,
    (state, newTodo: Todo) => [...state, newTodo]
  );

  const formAction = async (formData: FormData) => {
    const title = formData.get('title') as string;
    const newTodo = { id: Date.now(), title, completed: false };

    addOptimisticTodo(newTodo); // Mostrar de inmediato

    await addTodo(formData); // Procesar en servidor
  };

  return (
    <>
      <form action={formAction}>
        <input name="title" />
        <button type="submit">Agregar</button>
      </form>

      <ul>
        {optimisticTodos.map(todo => (
          <li key={todo.id}>{todo.title}</li>
        ))}
      </ul>
    </>
  );
}
```

---

## ⚠️ Limitaciones

1. **Solo en Servidor** - No puedes usar valores cliente dinámicos (sin pasar como argumento)
2. **Serializable** - Los argumentos deben ser serializables (no funciones, no clases)
3. **Tamaño de Payload** - Limitar el tamaño de datos que pasas
4. **Cookies** - Accesibles pero read-only en algunos contextos

---

## 🚫 Antipatrones

### ❌ Pasar funciones

```tsx
// ❌ MALO
const handleClick = () => {
  'use server';
  await db.post.create({...});
};

// ✅ BIEN
const handleClick = async () => {
  await createPost();
};
```

### ❌ No revalidar después de mutar

```tsx
// ❌ MALO
export async function createPost(data) {
  await db.post.create({ data });
  // Caché anticuado
}

// ✅ BIEN
export async function createPost(data) {
  await db.post.create({ data });
  revalidatePath('/posts'); // Actualizar caché
  revalidateTag('posts'); // O por tag
}
```

### ❌ Exponer secretos al cliente

```tsx
// ❌ MALO
'use client';
const apiKey = process.env.NEXT_PUBLIC_API_KEY; // En cliente!

// ✅ BIEN
'use server';
const apiKey = process.env.API_KEY; // En servidor, seguro
```

---

## 📊 Comparativa: Server Actions vs API Routes

| Aspecto | Server Actions | API Routes |
|--------|---|---|
| **Definición** | `'use server'` | `route.ts` en `/api` |
| **Invocación** | `<form action={}>` | `fetch('/api/...')` |
| **Tipo** | Reutilizable | Endpoint HTTP |
| **Curva aprendizaje** | Baja | Media |
| **Flexibilidad** | Alta (directa) | Media |
| **Debugging** | Más fácil | Más fácil (HTTP) |
| **Mejor para** | Formularios, mutaciones | APIs públicas |

---

## 🔗 Enlaces Relacionados

- [[App Router.md|App Router]]
- [[Data-Fetching.md|Data Fetching]]
- [[Performance.md|Performance]]
- [[_README.md|Next.js Principal]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con patrones y ejemplos |
