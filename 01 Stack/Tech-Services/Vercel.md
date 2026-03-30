---
title: Vercel - Plataforma de Deployment
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - vercel
  - deployment
  - nextjs
  - hosting
  - cdn
  - edge-functions
category: "01 Stack"
subcategory: "Tech-Services"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🚀 Vercel - Plataforma de Deployment

Plataforma oficial de Next.js optimizada para desarrollo, preview y producción. Infraestructura global con CDN, Edge Functions y serverless.

---

## 🎯 ¿Qué es Vercel?

Vercel es una plataforma de hosting y deployment especializada en **aplicaciones React y Next.js**. Proporciona:

- **Deployments automáticos** desde Git
- **Preview URLs** para cada PR
- **Edge Network** global (90+ regiones)
- **Serverless Functions** (Node.js, Python)
- **Edge Functions** (Cloudflare Workers-like)
- **Observability** integrado (Analytics, Web Vitals)

---

## 📋 Setup Inicial

### Opción 1: GitHub Integration (Recomendado)

```bash
# 1. Push código a GitHub
git push origin main

# 2. Ve a https://vercel.com
# 3. Click "Import Git Repository"
# 4. Selecciona repo desde GitHub
# 5. Configure environment variables
# 6. Click "Deploy"
```

### Opción 2: CLI

```bash
# Instalar Vercel CLI
npm i -g vercel

# Login
vercel login

# Desplegar proyecto
cd mi-app
vercel

# Configurar dominio
vercel domains add example.com
```

### Opción 3: Manual (Git Push to Deploy)

```bash
# Dentro de Settings → Git:
# - Connected Git Repository
# - Production Branch: main
# - Deploy on Push: enabled

git push origin main # ¡Deploy automático!
```

---

## 🔧 Configuración con vercel.json

```json
{
  "buildCommand": "npm run build",
  "installCommand": "npm install",
  "env": {
    "DATABASE_URL": "@database_url"
  },
  "envPrefix": "NEXT_PUBLIC_",
  "regions": ["sfo1", "iad1"],
  "functions": {
    "api/**/*.ts": {
      "maxDuration": 30,
      "memory": 512
    }
  },
  "redirects": [
    {
      "source": "/blog/:path*",
      "destination": "https://blog.example.com/:path*"
    }
  ],
  "rewrites": [
    {
      "source": "/api/:path*",
      "destination": "https://backend.example.com/api/:path*"
    }
  ]
}
```

---

## 🌍 Deployments y Branches

### Production

Rama designada (default: `main`) se deploya automáticamente:

```
main branch push
    ↓
Build & Test
    ↓
Deploy a vercel.com (dominio de producción)
    ↓
Activado (live)
```

### Preview

Cada PR crea una Preview URL:

```
PR abierto
    ↓
Build automático
    ↓
URL única generada: https://mi-app-pr-123.vercel.app
    ↓
Comentario automático en PR
```

### Staging (Opcional)

```bash
# Rama develop → staging environment
# En Vercel Dashboard:
# Settings → Git → Deploy Previews
# - Production Branch: main
# - Preview Branch: develop (puedes agregar)
```

---

## 🔐 Environment Variables

### Local Development

```bash
# .env.local (no commitear, no versionado)
DATABASE_URL=postgresql://localhost/dev
API_SECRET=local-secret-key
JWT_SECRET=local-jwt-secret

# .env (público, versionado)
NEXT_PUBLIC_API_URL=https://api.dev.example.com
```

### Vercel Dashboard

**Settings → Environment Variables:**

```
Name: DATABASE_URL
Value: postgresql://prod-user:pass@prod.db.com/prod
Environments: ✓ Production  ☐ Preview  ☐ Development
```

**Variables por Entorno:**

```
STRIPE_KEY_PRODUCTION=pk_live_xxxxx
STRIPE_KEY_PREVIEW=pk_test_xxxxx
STRIPE_KEY_DEVELOPMENT=pk_test_xxxxx
```

### CLI

```bash
# Listar variables
vercel env list

# Agregar variable
vercel env add DATABASE_URL
# Selecciona entorno y escribe valor

# Hacer pull local
vercel env pull .env.local
```

---

## 🧪 Previews and Deployments

### Evitar Deploy de PRs

```yaml
# vercel.json
{
  "github": {
    "enabled": false,  // Deshabilitar auto-deploy de PRs
    "silent": true
  }
}
```

### Deploy Manual desde CLI

```bash
# Preview
vercel --prod=false

# Production
vercel --prod

# Con mensaje
vercel --prod --message "Hotfix for issue #123"
```

### Rollback

```bash
vercel rollback

# O especificar deployment anterior:
vercel list
vercel promote <deployment-id>
```

---

## ⚡ Edge Functions

Código que corre en la borde de la red (cerca del usuario):

```typescript
// pages/api/hello.ts (Serverless Function)
export default function handler(req: NextApiRequest, res: NextApiResponse) {
  return res.status(200).json({ message: 'Hola' });
}

// lib/middleware.ts (Edge Middleware)
import { NextRequest, NextResponse } from 'next/server';

export function middleware(request: NextRequest) {
  // Ejecuta ANTES de tu aplicación
  if (request.nextUrl.pathname === '/admin' && !isAdmin(request)) {
    return NextResponse.redirect(new URL('/login', request.url));
  }
  return NextResponse.next();
}

export const config = {
  matcher: ['/admin/:path*'],
};
```

**Casos de Uso:**
- Authentication
- Redirects
- Rate limiting
- A/B testing

---

## 📊 Analytics y Monitoring

### Web Vitals

```tsx
// app/layout.tsx
import { Analytics } from '@vercel/analytics/react';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics /> {/* Recolecta Core Web Vitals */}
      </body>
    </html>
  );
}
```

Vercel Dashboard → Analytics:
- Largest Contentful Paint (LCP)
- First Input Delay (FID)
- Cumulative Layout Shift (CLS)
- First Byte Time (TTFB)

### Speed Insights

```tsx
import { SpeedInsights } from "@vercel/speed-insights/next"

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <SpeedInsights />
      </body>
    </html>
  );
}
```

---

## 🔄 Automatic Git Integration

### Conexiones Soportadas

- ✅ GitHub
- ✅ GitLab
- ✅ Bitbucket
- ✅ Gitea

### Comportamientos Automáticos

```
Commit a main
    ↓
Webhook enviado a Vercel
    ↓
Build iniciado automáticamente
    ↓
Tests ejecutados
    ↓
Deploy si todo OK
    ↓
Actualizar dominio de producción
```

### Ignorar Deployments

```bash
# Commit message
git commit -m "chore: update README [skip ci]"

# Entonces Vercel NO deployará
```

---

## 🎯 Dominios y SSL

### Dominio Personalizado

```bash
# CLI
vercel domains add example.com

# O Dashboard:
# Settings → Domains → Add
```

### Configuración DNS

Vercel proporciona recordes NS:

```
Nombre: example.com
Type: NS
Valor: ns1.vercel.com
Valor: ns2.vercel.com
```

O CNAME para subdomino:

```
Nombre: app.example.com
Type: CNAME
Valor: cname.vercel.com
```

### SSL/TLS

Automático y gratuito con Let's Encrypt.

---

## 📦 Build Optimization

### Build Time

Vercel optimiza automáticamente:
- Caching inteligente
- Parallelize builds
- Next.js optimizations

```bash
# Ver logs de build
vercel logs <deployment-id>

# Duración típica: 1-3 minutos
```

### Cache Control

```typescript
// next.config.js
module.exports = {
  onDemandEntries: {
    maxInactiveAge: 60 * 1000, // 1 minuto
    pagesBufferLength: 5,
  },
};
```

---

## 🚨 Common Issues

### Issue 1: Build Failures

```bash
# Error: Module not found
# Solución: npm install en local y commitear package-lock.json

npm install
git add package-lock.json
git commit -m "fix: update lockfile"
git push
```

### Issue 2: Environment Variables No Funcionar

```bash
# Problema: Variable no visible en build
# Solución: Usar NEXT_PUBLIC_ para variables públicas

# .env.local
NEXT_PUBLIC_API_URL=... ✅ Visible en cliente
API_SECRET=...             ✅ Solo servidor
```

### Issue 3: Timeouts

```json
{
  "functions": {
    "api/**/*.ts": {
      "maxDuration": 60  // Máximo 60 segundos
    }
  }
}
```

---

## 💰 Pricing

| Plan | Precio | Features |
|------|--------|----------|
| **Hobby** | Gratis | 3 concurrent builds, 100GB/mes bandwidth |
| **Pro** | $20/mes | Unlimited builds, 1TB/mes bandwidth |
| **Enterprise** | Custom | SSO, SLA, support 24/7 |

---

## 🔗 Enlaces Relacionados

- [[MongoDB.md|MongoDB]]
- [[Netlify.md|Netlify]]
- [[../Next.js/Deployment.md|Next.js Deployment]]
- [[../../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con setup, CLI y ejemplos |
