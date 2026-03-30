---
title: Deployment en Next.js
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - nextjs
  - deployment
  - hosting
  - vercel
  - docker
  - ci-cd
category: "01 Stack"
subcategory: "Next.js"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🚀 Deployment en Next.js 14+

Desplegar a producción: Vercel, auto-hosting, Docker, y estrategias avanzadas.

---

## 🌐 Vercel (Recomendado)

La plataforma oficial de Next.js. Optimizada y lista para producción.

### Setup Inicial

```bash
# 1. Conectar repositorio
git push origin main

# 2. En Vercel Dashboard:
# - Importar proyecto de GitHub
# - Variables de entorno automáticas
# - Deploy automático en push

# O CLI:
npm i -g vercel
vercel
```

### Variables de Entorno

```bash
# .env.local (no commitear)
DATABASE_URL=postgresql://...
API_SECRET=xxxxx

# .env.production (visibles)
NEXT_PUBLIC_API_URL=https://api.example.com
```

En Vercel Dashboard → Settings → Environment Variables

### Previews y Staging

Vercel crea automáticamente:
- **Preview URL** por cada PR
- **Production** en main branch
- **Staging** en rama designada

```typescript
// Detectar entorno
const apiUrl = process.env.NEXT_PUBLIC_API_URL || 'http://localhost:3000';
```

### Vercel Analytics

```tsx
// app/layout.tsx
import { Analytics } from '@vercel/analytics/react';
import { SpeedInsights } from '@vercel/speed-insights/next';

export default function RootLayout({ children }) {
  return (
    <html>
      <body>
        {children}
        <Analytics />
        <SpeedInsights />
      </body>
    </html>
  );
}
```

---

## 🏠 Self-Hosting (Alternativa)

### Requisitos

- Node.js 18.17+
- Base de datos (PostgreSQL, MongoDB, etc.)
- Servidor (DigitalOcean, AWS, etc.)
- Dominio

### Build para Producción

```bash
npm run build

# Esto genera:
# - .next/standalone (app compilada)
# - .next/static (assets)
# - public/ (archivos estáticos)
```

### Opción 1: Node.js Nativo

```bash
# 1. Build
npm run build

# 2. Instalar dependencies en producción
npm ci --omit=dev

# 3. Start
NODE_ENV=production node .next/standalone/server.js
```

### Opción 2: Docker

```dockerfile
# Dockerfile
FROM node:20-alpine AS base

# Stage 1: Dependencies
FROM base AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --only=production

# Stage 2: Build
FROM base AS builder
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 3: Runtime
FROM base AS runner
WORKDIR /app

ENV NODE_ENV=production

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000
ENV PORT=3000

CMD ["node", "server.js"]
```

```bash
# Build y run
docker build -t my-app .
docker run -p 3000:3000 -e DATABASE_URL=... my-app
```

### Opción 3: Docker Compose

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgresql://user:pass@db:5432/appdb
      - NODE_ENV=production
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      - POSTGRES_PASSWORD=secretpassword
      - POSTGRES_DB=appdb
    volumes:
      - db_data:/var/lib/postgresql/data

volumes:
  db_data:
```

```bash
docker-compose up -d
```

---

## 🔄 CI/CD Pipeline

### GitHub Actions

```yaml
# .github/workflows/deploy.yml
name: Deploy to Vercel

on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint
      - run: npm run test
      - run: npm run build

  deploy:
    needs: test
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - uses: actions/checkout@v3
      - uses: vercel/action@v4
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
```

---

## 📊 Providers Alternativos

### Netlify

```toml
# netlify.toml
[build]
  command = "npm run build"
  functions = "/.netlify/functions"

[[redirects]]
  from = "/*"
  to = "/index.html"
  status = 200
```

```bash
npm run build
netlify deploy --prod --dir=.next
```

### AWS Amplify

```bash
amplify init
amplify add hosting
amplify push

# O conectar desde Git en AWS Amplify console
```

### Render

```yaml
# render.yaml
services:
  - type: web
    name: my-app
    env: node
    buildCommand: npm run build
    startCommand: npm run start
    envVars:
      - key: NODE_ENV
        value: production
```

### Railway

```bash
railway link
railway up
```

---

## 📦 Static Export

Para sitios estáticos sin servidor:

```typescript
// next.config.js
/** @type {import('next').NextConfig} */
const nextConfig = {
  output: 'export',
  images: {
    unoptimized: true,
  },
};

module.exports = nextConfig;
```

```bash
npm run build
# Genera: out/ (HTML estático)
```

**Limitaciones:**
- No puedes usar Server Components
- No API Routes
- No Incremental Static Regeneration (ISR)
- No On-demand Revalidation

---

## 🔐 Seguridad en Producción

### Environment Variables

```bash
# .env.local (git ignored)
DATABASE_URL=postgresql://...
API_SECRET=super-secret-key
JWT_SECRET=another-secret

# .env (público, en git)
NEXT_PUBLIC_API_URL=https://api.example.com
NEXT_PUBLIC_SITE_URL=https://example.com
```

### Headers de Seguridad

```typescript
// next.config.js
const securityHeaders = [
  {
    key: 'X-Content-Type-Options',
    value: 'nosniff'
  },
  {
    key: 'X-Frame-Options',
    value: 'DENY'
  },
  {
    key: 'X-XSS-Protection',
    value: '1; mode=block'
  },
  {
    key: 'Referrer-Policy',
    value: 'strict-origin-when-cross-origin'
  },
  {
    key: 'Permissions-Policy',
    value: 'camera=(), microphone=(), geolocation=()'
  }
];

module.exports = {
  async headers() {
    return [
      {
        source: '/(.*)',
        headers: securityHeaders,
      }
    ];
  }
};
```

### Rate Limiting

```tsx
// app/api/route.ts
import { Ratelimit } from '@upstash/ratelimit';
import { Redis } from '@upstash/redis';

const ratelimit = new Ratelimit({
  redis: Redis.fromEnv(),
  limiter: Ratelimit.slidingWindow(10, '10 s'),
});

export async function POST(request: Request) {
  const identifier = request.headers.get('x-forwarded-for');
  const { success } = await ratelimit.limit(identifier!);

  if (!success) {
    return new Response('Rate limit exceeded', { status: 429 });
  }

  // Procesar request
}
```

---

## ✅ Pre-deployment Checklist

- [ ] Environment variables configuradas
- [ ] Build exitoso localmente (`npm run build`)
- [ ] Tests pasan (`npm run test`)
- [ ] Linting limpio (`npm run lint`)
- [ ] No console.logs de debug
- [ ] Images optimizadas
- [ ] Fonts optimizadas
- [ ] Sitemaps.xml y robots.txt
- [ ] Analytics configurado
- [ ] Error handling completo
- [ ] Trailing slashes configurados
- [ ] Redirecciones HTTP establecidas
- [ ] CORS configurado (si necesario)
- [ ] Rate limiting activado
- [ ] Backups de BD automatizados

---

## 🚨 Monitoreo Post-deployment

### Logging

```tsx
// Usar servicio como Sentry, LogRocket, etc.
import * as Sentry from "@sentry/nextjs";

export async function getServerSideProps() {
  try {
    const data = await fetchData();
    return { props: { data } };
  } catch (error) {
    Sentry.captureException(error);
    return { notFound: true };
  }
}
```

### Alertas

- Configurar alertas para errores 5xx
- Monitorear Core Web Vitals
- Alertas de uptime
- Notificaciones de deployments

---

## 🔗 Enlaces Relacionados

- [[App Router.md|App Router]]
- [[Performance.md|Performance]]
- [[Server-Actions.md|Server Actions]]
- [[_README.md|Next.js Principal]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con múltiples estrategias |
