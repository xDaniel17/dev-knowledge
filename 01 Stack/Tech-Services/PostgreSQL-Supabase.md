---
title: Supabase - Firebase Open Source Alternative
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - supabase
  - database
  - backend
  - postgresql
  - authentication
  - realtime
  - storage
  - saas
category: "01 Stack"
subcategory: "Tech-Services"
author: Daniel
version: 2.0.0
status: active
related:
  - PostgreSQL-Supabase
  - Firebase
---

# 🚀 Supabase - Firebase Open Source Alternative

Supabase es una plataforma **open-source** que proporciona PostgreSQL como servicio con características adicionales: autenticación, storage, edge functions, real-time subscriptions y más. Es una alternativa a Firebase con la potencia de PostgreSQL.

---

## 🎯 ¿Qué es Supabase?

```
┌─────────────────────────────────────────────────────────┐
│                      SUPABASE                           │
├─────────────────────────────────────────────────────────┤
│  PostgreSQL (relacional, potente, escalable)            │
│  + Auth (múltiples proveedores)                        │
│  + Storage (archivos, imágenes, PDFs)                   │
│  + Realtime (subscriptions live)                        │
│  + Edge Functions (serverless)                         │
│  + Vector (búsqueda semántica)                         │
│  = Backend completo como servicio                      │
└─────────────────────────────────────────────────────────┘
```

### Características Principales

| Feature | Descripción |
|---------|-------------|
| **Database** | PostgreSQL 15+ hosted |
| **Auth** | Email, OAuth, SMS, Magic Links |
| **Storage** |Buckets para archivos |
| **Realtime** | WebSocket subscriptions |
| **Edge Functions** | Serverless TypeScript/JS |
| **Vector** | Búsqueda de embeddings |
| **AI** | Integración con LLMs |
| **Auto APIs** | REST y GraphQL automática |

---

## 📋 Setup Inicial

### Crear Proyecto

```bash
# 1. Ve a https://supabase.com/dashboard
# 2. Click "New Project"
# 3. Selecciona organización
# 4. Configura:
#    - Project name: my-app
#    - Database Password: (genera una segura)
#    - Region: más cercana a tus usuarios
# 5. Click "Create new project"
```

### Instalar Cliente

```bash
npm install @supabase/supabase-js
# o con yarn
yarn add @supabase/supabase-js
# o con pnpm
pnpm add @supabase/supabase-js
```

### Configurar Variables de Entorno

```bash
# .env.local
NEXT_PUBLIC_SUPABASE_URL=https://xxxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJhbGciOiJIUzI1NiIs...
# La ANON KEY es pública (usada en cliente)
# Para operaciones de servidor, usa el SERVICE_ROLE_KEY
SUPABASE_SERVICE_ROLE_KEY=eyJhbGciOiJIUzI1NiIs...
```

### Inicializar Cliente

```typescript
// lib/supabase.ts
import { createClient } from '@supabase/supabase-js'

const supabaseUrl = process.env.NEXT_PUBLIC_SUPABASE_URL!
const supabaseAnonKey = process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!

export const supabase = createClient(supabaseUrl, supabaseAnonKey)

// Cliente para servidor (con privilegios elevados)
export const supabaseAdmin = createClient(supabaseUrl, process.env.SUPABASE_SERVICE_ROLE_KEY!)
```

---

## 🗄️ Base de Datos

### Table Editor (Dashboard)

Desde el dashboard puedes:
- Crear tablas visualmente
- Definir columnas y tipos
- Establecer relaciones (foreign keys)
- Configurar RLS policies
- Ver y editar datos

### SQL Editor

```sql
-- Crear tabla de perfiles
CREATE TABLE profiles (
  id UUID REFERENCES auth.users(id) ON DELETE CASCADE PRIMARY KEY,
  username TEXT UNIQUE NOT NULL,
  full_name TEXT,
  avatar_url TEXT,
  bio TEXT,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- Habilitar RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Policy: usuarios pueden ver perfiles públicos
CREATE POLICY "Profiles are viewable by everyone"
  ON profiles FOR SELECT
  USING (true);

-- Policy: usuarios solo pueden actualizar su propio perfil
CREATE POLICY "Users can update own profile"
  ON profiles FOR UPDATE
  USING (auth.uid() = id);
```

### Tipos de Datos PostgreSQL

```sql
-- UUID (recomendado para IDs)
id UUID DEFAULT gen_random_uuid()

-- Timestamps
created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
updated_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()

-- JSONB (datos flexibles)
metadata JSONB DEFAULT '{}'
preferences JSONB

-- Arrays
tags TEXT[] DEFAULT '{}'

-- Enums
CREATE TYPE status AS ENUM ('active', 'inactive', 'pending');

-- Textos largos
description TEXT
```

---

## 🔐 Autenticación

### Email y Password

```typescript
// Registro
const { data, error } = await supabase.auth.signUp({
  email: 'user@example.com',
  password: 'secure-password',
  options: {
    data: {
      username: 'john_doe'
    }
  }
})

// Login
const { data, error } = await supabase.auth.signInWithPassword({
  email: 'user@example.com',
  password: 'secure-password',
})

// Logout
const { error } = await supabase.auth.signOut()

// Obtener sesión actual
const { data: { session } } = supabase.auth.getSession()

// Obtener usuario actual
const { data: { user } } = await supabase.auth.getUser()
```

### OAuth Providers

```typescript
// Google
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'google',
  options: {
    redirectTo: `${window.location.origin}/auth/callback`
  }
})

// GitHub
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'github',
  options: {
    redirectTo: `${window.location.origin}/auth/callback`
  }
})

// Apple
const { data, error } = await supabase.auth.signInWithOAuth({
  provider: 'apple'
})

// Twitter / Facebook / Azure / Discord / Spotify / etc.
```

### Magic Links

```typescript
const { error } = await supabase.auth.signInWithOtp({
  email: 'user@example.com',
  options: {
    emailRedirectTo: `${window.location.origin}/auth/callback`
  }
})
```

### Callback Handler (Next.js)

```typescript
// app/auth/callback/route.ts
import { createClient } from '@/lib/supabase'
import { NextResponse } from 'next/server'

export async function GET(request: Request) {
  const { searchParams, origin } = new URL(request.url)
  const code = searchParams.get('code')
  const next = searchParams.get('next') ?? '/dashboard'

  if (code) {
    const supabase = createClient()
    const { error } = await supabase.auth.exchangeCodeForSession(code)
    if (!error) {
      return NextResponse.redirect(`${origin}${next}`)
    }
  }

  return NextResponse.redirect(`${origin}/login?error=auth`)
}
```

### Middleware (protección de rutas)

```typescript
// middleware.ts
import { createServerClient } from '@supabase/ssr'
import { NextResponse, type NextRequest } from 'next/server'

export async function middleware(request: NextRequest) {
  let supabaseResponse = NextResponse.next({
    request,
  })

  const supabase = createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return request.cookies.getAll()
        },
        setAll(cookiesToSet) {
          cookiesToSet.forEach(({ name, value }) =>
            request.cookies.set(name, value)
          )
          supabaseResponse = NextResponse.next({
            request,
          })
          cookiesToSet.forEach(({ name, value, options }) =>
            supabaseResponse.cookies.set(name, value, options)
          )
        },
      },
    }
  )

  const { data: { user } } = await supabase.auth.getUser()

  // Proteger rutas
  if (!user && !request.nextUrl.pathname.startsWith('/auth')) {
    const url = request.nextUrl.clone()
    url.pathname = '/auth/login'
    return NextResponse.redirect(url)
  }

  return supabaseResponse
}

export const config = {
  matcher: ['/((?!_next/static|_next/image|favicon.ico|.*\\.(?:svg|png|jpg|jpeg|gif|webp)$).*)'],
}
```

### Monitor Auth State (React)

```typescript
// components/AuthProvider.tsx
'use client'

import { createContext, useContext, useEffect, useState } from 'react'
import { User } from '@supabase/supabase-js'
import { createClient } from '@/lib/supabase'

const Context = createContext<{ user: User | null }>({ user: null })

export const useUser = () => useContext(Context)

export function AuthProvider({ children }: { children: React.ReactNode }) {
  const [user, setUser] = useState<User | null>(null)

  useEffect(() => {
    const supabase = createClient()
    
    const { data: { subscription } } = supabase.auth.onAuthStateChange((_event, session) => {
      setUser(session?.user ?? null)
    })

    return () => subscription.unsubscribe()
  }, [])

  return (
    <Context.Provider value={{ user }}>
      {children}
    </Context.Provider>
  )
}
```

---

## 🔄 Realtime

### Configuración

En el dashboard: **Database → Replication** → Selecciona tablas para replicar.

### Suscripciones en React

```typescript
// hooks/useRealtime.ts
import { useEffect, useState } from 'react'
import { createClient } from '@/lib/supabase'

export function useRealtime<T>(table: string, query?: any) {
  const [data, setData] = useState<T[]>([])
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    const supabase = createClient()

    // Fetch inicial
    const fetchData = async () => {
      let q = supabase.from(table).select('*')
      if (query) q = query(q)
      const { data: result } = await q
      setData(result || [])
      setLoading(false)
    }

    fetchData()

    // Suscripción realtime
    const channel = supabase
      .channel(`${table}-changes`)
      .on(
        'postgres_changes',
        { event: '*', schema: 'public', table },
        (payload) => {
          if (payload.eventType === 'INSERT') {
            setData(prev => [payload.new as T, ...prev])
          } else if (payload.eventType === 'UPDATE') {
            setData(prev => prev.map(item => 
              (item as any).id === payload.new.id ? payload.new as T : item
            ))
          } else if (payload.eventType === 'DELETE') {
            setData(prev => prev.filter(item => 
              (item as any).id !== payload.old.id
            ))
          }
        }
      )
      .subscribe()

    return () => {
      supabase.removeChannel(channel)
    }
  }, [table])

  return { data, loading }
}

// Uso
const { data: posts, loading } = useRealtime('posts')
```

### Presencia (colaboración en tiempo real)

```typescript
const channel = supabase.channel('room-1')

channel
  .on('presence', { event: 'sync' }, () => {
    const state = channel.presenceState()
    console.log('Online users:', state)
  })
  .on('presence', { event: 'join' }, ({ key, newPresences }) => {
    console.log('User joined:', newPresences)
  })
  .on('presence', { event: 'leave' }, ({ key, leftPresences }) => {
    console.log('User left:', leftPresences)
  })
  .subscribe(async (status) => {
    if (status === 'SUBSCRIBED') {
      await channel.track({
        user_id: user.id,
        online_at: new Date().toISOString(),
      })
    }
  })
```

---

## 📦 Storage

### Crear Bucket (Dashboard)

**Storage → New Bucket** → Nombre: `avatars`, Public: ✓

### Upload

```typescript
// Subir archivo
const uploadAvatar = async (userId: string, file: File) => {
  const { data, error } = await supabase.storage
    .from('avatars')
    .upload(`${userId}/avatar.jpg`, file, {
      cacheControl: '3600',
      upsert: true,
    })

  if (error) throw error
  return data
}

// Subir con progreso
const uploadWithProgress = async (file: File) => {
  const { data, error } = await supabase.storage
    .from('documents')
    .upload(file.name, file, {
      cacheControl: '3600',
      upsert: false,
    })

  return { data, error }
}
```

### Descargar

```typescript
// URL pública (para buckets públicos)
const { data } = supabase.storage
  .from('avatars')
  .getPublicUrl('user-123/avatar.jpg')

// URL firmada (temporal, para buckets privados)
const { data, error } = await supabase.storage
  .from('documents')
  .createSignedUrl('report.pdf', 3600) // expira en 1 hora
```

### Descargar como Blob

```typescript
const downloadFile = async (bucket: string, path: string) => {
  const { data, error } = await supabase.storage
    .from(bucket)
    .download(path)

  if (error) throw error
  return URL.createObjectURL(data)
}
```

### Eliminar

```typescript
const { error } = await supabase.storage
  .from('avatars')
  .remove(['user-123/avatar.jpg'])
```

### Storage Policies (RLS para archivos)

```sql
-- Solo usuarios autenticados pueden subir
CREATE POLICY "Authenticated users can upload"
  ON storage.objects FOR INSERT
  TO authenticated
  WITH CHECK (bucket_id = 'avatars');

-- Cualquiera puede ver (bucket público)
CREATE POLICY "Public access to avatars"
  ON storage.objects FOR SELECT
  TO public
  USING (bucket_id = 'avatars');

-- Usuarios solo eliminan sus propios archivos
CREATE POLICY "Users can delete own files"
  ON storage.objects FOR DELETE
  TO authenticated
  USING (auth.uid()::text = (storage.foldername(name))[1]);
```

---

## ⚡ Edge Functions

### Deployar Function

```bash
# Instalar CLI
npm install -g supabase

# Inicializar en proyecto
supabase functions new my-function

# Desplegar
supabase functions deploy my-function
```

### Código de Function

```typescript
// supabase/functions/my-function/index.ts
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

serve(async (req) => {
  const supabase = createClient(
    Deno.env.get('SUPABASE_URL')!,
    Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!
  )

  const { data, error } = await supabase
    .from('profiles')
    .select('*')
    .limit(10)

  return new Response(
    JSON.stringify({ data, error }),
    { headers: { 'Content-Type': 'application/json' } }
  )
})
```

### Invocar desde Cliente

```typescript
const { data, error } = await supabase.functions.invoke('my-function', {
  body: { userId: '123' }
})
```

---

## 🤖 AI & Vectores

### Búsqueda Semántica (pgvector)

```sql
-- Habilitar extensión vector
CREATE EXTENSION vector;

-- Crear tabla con embedding
CREATE TABLE documents (
  id BIGSERIAL PRIMARY KEY,
  content TEXT NOT NULL,
  embedding vector(1536)
);

-- Búsqueda por similitud
SELECT content, 1 - (embedding <=> $1::vector) AS similarity
FROM documents
ORDER BY embedding <=> $1::vector
LIMIT 5;
```

### Edge Function para Embeddings

```typescript
// supabase/functions/search/index.ts
serve(async (req) => {
  const { query } = await req.json()
  const supabase = createClient(/* ... */)
  
  // Generar embedding (usando OpenAI, Anthropic, etc.)
  const embedding = await generateEmbedding(query)
  
  // Buscar documentos similares
  const { data } = await supabase.rpc('match_documents', {
    query_embedding: embedding,
    match_threshold: 0.8,
    match_count: 5
  })
  
  return Response.json({ results: data })
})
```

---

## 🔒 Row Level Security (RLS)

### Patrones Comunes

```sql
-- Enable RLS
ALTER TABLE profiles ENABLE ROW LEVEL SECURITY;

-- Todos pueden leer
CREATE POLICY "Public read" ON profiles
  FOR SELECT USING (true);

-- Solo owner puede actualizar
CREATE POLICY "Owner update" ON profiles
  FOR UPDATE USING (auth.uid() = id);

-- Nuevo perfil al crear usuario (trigger)
CREATE OR REPLACE FUNCTION public.handle_new_user()
RETURNS trigger AS $$
BEGIN
  INSERT INTO public.profiles (id, username)
  VALUES (new.id, new.raw_user_meta_data->>'username');
  RETURN new;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW EXECUTE FUNCTION public.handle_new_user();
```

### Verificar Policies

```sql
-- Listar policies de una tabla
SELECT policyname, cmd, qual, with_check
FROM pg_policies
WHERE tablename = 'profiles';

-- Testear policy
EXPLAIN ANALYZE
SELECT * FROM profiles WHERE id = auth.uid();
```

---

## 💰 Pricing

| Plan | Precio | Database | Bandwidth | Auth |
|------|--------|----------|-----------|------|
| **Free** | $0 | 500MB | 2GB/mo | 50k MAU |
| **Pro** | $25/mes | 8GB | 50GB/mo | 100k MAU |
| **Team** | $599/mes | 100GB | 1TB/mo | Unlimited |

### Límites Free
- 500MB database
- 1GB storage
- 2GB bandwidth transfer
- 50k monthly active users
- 50MB max file size
- No AI features

---

## 🛠️ Integraciones

### Next.js App Router

```typescript
// lib/supabase/server.ts (Server Component)
import { createServerClient } from '@supabase/ssr'
import { cookies } from 'next/headers'

export async function createClient() {
  const cookieStore = await cookies()

  return createServerClient(
    process.env.NEXT_PUBLIC_SUPABASE_URL!,
    process.env.NEXT_PUBLIC_SUPABASE_ANON_KEY!,
    {
      cookies: {
        getAll() {
          return cookieStore.getAll()
        },
        setAll(cookiesToSet) {
          try {
            cookiesToSet.forEach(({ name, value, options }) =>
              cookieStore.set(name, value, options)
            )
          } catch {}
        },
      },
    }
  )
}

// Uso en Server Component
const supabase = await createClient()
const { data: profiles } = await supabase.from('profiles').select('*')
```

### Prisma Integration

```typescript
// schema.prisma
datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        String   @id @default (dbgenerated("gen_random_uuid()"))
  email     String   @unique
  createdAt DateTime @default(now())
  profile   Profile?
}

model Profile {
  id        String  @id @default (dbgenerated("gen_random_uuid()"))
  userId    String  @unique
  user      User    @relation(fields: [userId], references: [id])
  username  String
}
```

### Flutter / React Native

```dart
// Flutter
import 'package:supabase_flutter/supabase_flutter.dart';

await Supabase.initialize(
  url: 'https://xxx.supabase.co',
  anonKey: 'eyJhbGci...',
);

final supabase = Supabase.instance.client;
```

---

## 🔗 Dashboard Features

```
┌────────────────────────────────────────────────────────┐
│  SUPABASE DASHBOARD                                    │
├────────────────────────────────────────────────────────┤
│                                                        │
│  📊 Overview    - Métricas del proyecto               │
│  📝 Table Editor - CRUD visual de tablas               │
│  💿 SQL Editor   - Consultas directas                   │
│  🔐 Authentication - Usuarios, providers, logs          │
│  📦 Storage     - Buckets y archivos                   │
│  🔄 Replication - Config realtime                      │
│  ⚡ Edge Functions - Serverless functions              │
│  🔧 API         - Docs auto-generadas                 │
│  📈 Logs        - Logs de queries y auth               │
│                                                        │
└────────────────────────────────────────────────────────┘
```

---

## 🚨 Troubleshooting

| Issue | Solución |
|-------|----------|
| `CORS error` | Agregar dominio en Settings → Auth → Redirect URLs |
| `RLS denies access` | Verificar policies con `EXPLAIN ANALYZE` |
| `Auth session expired` | Refrescar token o re-autenticar |
| `Realtime not working` | Habilitar replication en Database → Replication |
| `Storage 404` | Verificar bucket policy y URL pública |
| `Slow queries` | Crear índices, usar `EXPLAIN ANALYZE` |

---

## 🔗 Enlaces Relacionados

- [[Vercel.md|Vercel]] - Deployment frontend
- [[Railway.md|Railway]] - Deployment backend alternativo
- [[Firebase.md|Firebase]] - Alternativa a Supabase
- [[MongoDB.md|MongoDB]] - Base de datos NoSQL
- [[../../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Expansión completa con Auth, Realtime, Storage, Edge Functions, AI |
| 1.0.0 | 2026-03-30 | OpenCode | Creación inicial PostgreSQL & Supabase |
