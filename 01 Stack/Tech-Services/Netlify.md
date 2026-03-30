---
title: Netlify - Hosting y Deployment
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - netlify
  - deployment
  - hosting
  - static
  - serverless
  - forms
category: "01 Stack"
subcategory: "Tech-Services"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🌐 Netlify - Hosting y Deployment

Plataforma de hosting para sitios estáticos, JAMstack y serverless functions. Alternativa flexible a Vercel.

---

## 🎯 ¿Qué es Netlify?

Netlify combina:
- **Git-based deployment** automático
- **Static hosting** con CDN global
- **Serverless functions** (Node.js, Python, Go)
- **Forms** integrado
- **Split testing** (A/B)
- **Analytics** y **monitoring**

---

## 🚀 Setup Inicial

### Opción 1: Dashboard (Recomendado)

```bash
# 1. Ve a netlify.com/signup
# 2. Login con GitHub
# 3. Click "New site from Git"
# 4. Selecciona repositorio
# 5. Configura build settings
# 6. Deploy!
```

### Opción 2: Netlify CLI

**macOS/Linux:**
```bash
npm install -g netlify-cli

netlify login
cd mi-app
netlify init

# Selecciona opciones:
# Build command: npm run build
# Publish directory: .next (o dist/)
```

**Windows (PowerShell):**
```powershell
npm install -g netlify-cli

netlify login
cd mi-app
netlify init
```

### Opción 3: Manual Deploy

```bash
# Build localmente
npm run build

# Deploy con Netlify CLI
netlify deploy --prod --dir=.next
```

---

## 📋 Configuración con netlify.toml

```toml
[build]
  command = "npm run build"
  functions = "netlify/functions"
  publish = ".next"

[build.environment]
  NODE_VERSION = "18"
  NODE_ENV = "production"

[functions]
  node_bundler = "esbuild"

[[redirects]]
  from = "/blog/:id"
  to = "https://blog.example.com/:id"
  status = 301

[[rewrites]]
  from = "/*"
  to = "/index.html"
  status = 200

[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-Content-Type-Options = "nosniff"

[[edge_functions]]
  path = "/api/*"
  function = "auth"
```

---

## 🔐 Environment Variables

### Dashboard

**Site settings → Build & deploy → Environment:**

```
Key: DATABASE_URL
Value: postgresql://...

Key: NEXT_PUBLIC_API_URL
Value: https://api.example.com
```

### CLI

```bash
# Ver variables
netlify env:list

# Agregar variable
netlify env:set DATABASE_URL "postgresql://..."

# Sincronizar local
netlify env:import .env.local
```

---

## 🛠️ Netlify Functions (Serverless)

### Crear Function

**netlify/functions/hello.ts:**
```typescript
import { Handler, HandlerEvent, HandlerContext } from '@netlify/functions';

const handler: Handler = async (event: HandlerEvent, context: HandlerContext) => {
  console.log('Event:', event);
  
  return {
    statusCode: 200,
    body: JSON.stringify({ message: 'Hola desde Netlify Function!' }),
  };
};

export { handler };
```

**Accesible en:** `/.netlify/functions/hello`

### Function con Query Parameters

```typescript
const handler: Handler = async (event: HandlerEvent) => {
  const { name } = event.queryStringParameters || {};
  
  return {
    statusCode: 200,
    body: JSON.stringify({ message: `Hola, ${name || 'Mundo'}!` }),
  };
};

export { handler };
```

### Function con POST

```typescript
const handler: Handler = async (event: HandlerEvent) => {
  if (event.httpMethod !== 'POST') {
    return { statusCode: 405, body: 'Method Not Allowed' };
  }
  
  const data = JSON.parse(event.body || '{}');
  const { email } = data;
  
  // Guardar en BD, enviar email, etc.
  
  return {
    statusCode: 201,
    body: JSON.stringify({ success: true, email }),
  };
};

export { handler };
```

### Next.js con Netlify

Usar Next.js `api/` routes en lugar de Netlify Functions:

```typescript
// app/api/hello/route.ts
export async function GET(request: Request) {
  return new Response(JSON.stringify({ message: 'Hello!' }), {
    status: 200,
    headers: { 'Content-Type': 'application/json' },
  });
}
```

---

## 📋 Netlify Forms

### HTML Form

```html
<form name="contact" method="POST" netlify>
  <label>
    Nombre:
    <input type="text" name="name" required />
  </label>
  
  <label>
    Email:
    <input type="email" name="email" required />
  </label>
  
  <label>
    Mensaje:
    <textarea name="message" required></textarea>
  </label>
  
  <button type="submit">Enviar</button>
</form>
```

### React Form

```tsx
'use client';

export function ContactForm() {
  const [submitted, setSubmitted] = useState(false);

  const handleSubmit = async (e: React.FormEvent<HTMLFormElement>) => {
    e.preventDefault();
    
    const form = e.currentTarget;
    const formData = new FormData(form);
    
    const response = await fetch('/', {
      method: 'POST',
      headers: { 'Content-Type': 'application/x-www-form-urlencoded' },
      body: new URLSearchParams(formData as any).toString(),
    });
    
    if (response.ok) {
      setSubmitted(true);
      form.reset();
    }
  };

  if (submitted) {
    return <p>¡Gracias por tu mensaje!</p>;
  }

  return (
    <form name="contact" onSubmit={handleSubmit}>
      <input name="name" type="text" required />
      <input name="email" type="email" required />
      <textarea name="message" required></textarea>
      <button type="submit">Enviar</button>
    </form>
  );
}
```

---

## 🎬 Preview y Production

### Preview Deployments

Cada push a rama no-producción crea preview:

```
netlify.toml:
[context.main]
  command = "npm run build"
  publish = "dist"

[context.develop]
  command = "npm run build:dev"
  publish = "dist-dev"
```

```bash
# main → Deploy a producción
# develop → Deploy preview
# feature/* → Deploy preview
```

### Branch Deploys

**Site settings → Build & deploy → Deploy contexts:**

```
Production branch: main
Deploy preview for pull requests: ON
Deploy previews for branch deploys: develop
```

---

## 💡 Redirecciones y Rewrites

### 301 Redirect (SEO)

```toml
[[redirects]]
  from = "/old-page"
  to = "/new-page"
  status = 301
```

### 200 Rewrite (Invisible)

```toml
[[rewrites]]
  from = "/api/*"
  to = "https://backend.example.com/api/:splat"
  status = 200
```

### Rewrite para SPA

```toml
[[rewrites]]
  from = "/*"
  to = "/index.html"
  status = 200
```

---

## 🔒 Security y Headers

```toml
[[headers]]
  for = "/*"
  [headers.values]
    X-Frame-Options = "DENY"
    X-Content-Type-Options = "nosniff"
    X-XSS-Protection = "1; mode=block"
    Referrer-Policy = "strict-origin-when-cross-origin"
    Permissions-Policy = "geolocation=(), microphone=()"
    Content-Security-Policy = "default-src 'self'; script-src 'self' 'unsafe-inline'"

[[headers]]
  for = "/api/*"
  [headers.values]
    "Access-Control-Allow-Origin" = "*"
    "Access-Control-Allow-Methods" = "GET, POST, PUT, DELETE"
```

---

## 📊 Analytics y Monitoring

### Netlify Analytics

```
Site settings → Analytics
- Page views
- Top pages
- Referrers
- Device breakdown
```

### Edge Functions Logs

```bash
netlify logs:tail

# Tail en tiempo real
```

---

## 🚀 Performance

### Caching

```toml
[[headers]]
  for = "/static/*"
  [headers.values]
    "Cache-Control" = "public, max-age=31536000, immutable"

[[headers]]
  for = "/images/*"
  [headers.values]
    "Cache-Control" = "public, max-age=2592000"

[[headers]]
  for = "/*.html"
  [headers.values]
    "Cache-Control" = "public, max-age=0, must-revalidate"
```

### Minificación

```toml
[build]
  command = "npm run build"
  # Netlify automáticamente minifica CSS y JS
```

---

## 🔄 Split Testing (A/B Testing)

```toml
[[redirects]]
  from = "/"
  to = "/version-a"
  status = 200
  force = false
  conditions = { browser = ["chrome"] }

[[redirects]]
  from = "/"
  to = "/version-b"
  status = 200
  force = false
  conditions = { browser = ["firefox"] }
```

---

## 🚫 Common Issues

### Build Failures

```bash
# Limpiar caché
netlify build --prod

# Ver logs
netlify logs

# Reinstalar dependencias
rm -rf node_modules package-lock.json
npm install
netlify build
```

### CORS Errors

```toml
[[headers]]
  for = "/*"
  [headers.values]
    "Access-Control-Allow-Origin" = "*"
```

### Función No Encontrada

```bash
# Verificar estructura
netlify/functions/
  └── hello.ts ✅

# Build local
netlify build

# Deploy
netlify deploy --prod
```

---

## 💰 Pricing

| Plan | Precio | Características |
|------|--------|-----------------|
| **Free** | $0 | Unlimited sites, 300 function invocations/mes |
| **Pro** | $19/mes | Unlimited functions, Priority support |
| **Business** | $99/mes | Team collaboration, Advanced analytics |

---

## 📊 Netlify vs Vercel

| Aspecto | Netlify | Vercel |
|--------|---------|--------|
| **Focus** | Static + Serverless | Next.js (React) |
| **Functions** | Soportadas | Soportadas |
| **Forms** | Integrado ✅ | No |
| **Ease** | Easy | Very Easy (Next.js) |
| **Pricing** | Generoso | Similar |
| **Best for** | JAMstack, Static | Next.js apps |

---

## 🔗 Enlaces Relacionados

- [[Vercel.md|Vercel]]
- [[MongoDB.md|MongoDB]]
- [[../Next.js/Deployment.md|Next.js Deployment]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con setup, functions, forms y ejemplos |
