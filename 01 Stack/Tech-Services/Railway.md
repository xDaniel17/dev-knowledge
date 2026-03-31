---
title: Railway - Plataforma de Deployment
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - railway
  - deployment
  - hosting
  - paas
  - databases
  - docker
category: "01 Stack"
subcategory: "Tech-Services"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🚂 Railway - Plataforma de Deployment

Plataforma de hosting multilenguaje con soporte nativo para bases de datos, Docker y despliegues automáticos desde Git.

---

## 🎯 ¿Qué es Railway?

Railway es una **Platform as a Service (PaaS)** que proporciona:

- **Deployments desde Git** o Docker
- **Bases de datos integradas** (PostgreSQL, MySQL, MongoDB, Redis)
- **Entornos múltiples** (development, staging, production)
- **Pricing por uso** (pay-as-you-go)
- **Soporte multilenguaje** (Node.js, Python, Go, Rust, Ruby, etc.)
- **Variables de entorno** gestionadas

---

## 📋 Setup Inicial

### Opción 1: GitHub Integration (Recomendado)

```bash
# 1. Ve a https://railway.app
# 2. Conecta tu cuenta de GitHub
# 3. Click "New Project" → "Deploy from GitHub repo"
# 4. Selecciona el repositorio
# 5. Railway detectará automáticamente el framework
# 6. Click "Deploy Now"
```

### Opción 2: CLI

```bash
# Instalar Railway CLI
npm install -g @railway/cli

# Login
railway login

# Inicializar proyecto
cd mi-app
railway init

# Desplegar
railway up

# Abrir dashboard
railway open
```

### Opción 3: Docker

```dockerfile
# Dockerfile
FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm ci --production
COPY . .
RUN npm run build
EXPOSE 3000
CMD ["npm", "start"]
```

---

## 🔧 Configuración de Proyectos

### Railway.toml

```toml
[build]
builder = "nixpacks"

[deploy]
numReplicas = 2
restartPolicyType = "OnFailure"
restartPolicyAmount = 10

[start]
command = "npm start"
path = "/"
```

### Variables de Entorno

```bash
# CLI
railway variables set DATABASE_URL=postgresql://...
railway variables set NODE_ENV=production

# O desde dashboard:
# Project → Variables → Add Variable
```

---

## 🗄️ Bases de Datos Integradas

### PostgreSQL

```bash
# Crear base de datos
railway add postgresql

# Connection string disponible como variable
# DATABASE_URL se injecta automáticamente
```

```typescript
import { Pool } from 'pg';

const pool = new Pool({
  connectionString: process.env.DATABASE_URL,
});
```

### MongoDB

```bash
# Crear base de datos MongoDB
railway add mongodb
```

### Redis

```bash
# Crear cache Redis
railway add redis
```

---

## 🌍 Entornos (Environments)

Railway proporciona **3 entornos** por defecto:

```
Development → Staging → Production
```

### Cambiar Entorno

```bash
# Listar entornos
railway environment list

# Cambiar entorno
railway environment default production
```

### Variables por Entorno

```bash
# Establecer variable en entorno específico
railway variables --environment production set API_KEY=xxx
```

---

## 🔄 Deployments

### Deployment Automático

```
Git push (main)
    ↓
Railway detecta cambio
    ↓
Build automático
    ↓
Health checks
    ↓
Blue/Green deployment
    ↓
Dominio de producción actualizado
```

### Deployment Manual

```bash
# Deploy actual directory
railway up

# Con mensaje
railway up --detached --message "Fix login bug"

# Ver logs
railway logs
```

### Rollback

```bash
# Listar deployments
railway deployments

# Rollback a deployment anterior
railway rollback <deployment-id>
```

---

## 🐳 Docker en Railway

### Dockerfile Personalizado

```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .
RUN npm run build

EXPOSE 3000

CMD ["node", "dist/index.js"]
```

### docker-compose.yml

```yaml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=${DATABASE_URL}
    depends_on:
      - db
      - redis

  db:
    image: postgres:15
    environment:
      POSTGRES_PASSWORD: secret
      POSTGRES_DB: myapp

  redis:
    image: redis:7-alpine
```

---

## 🔐 Dominios y SSL

### Dominio Automático

Railway provee un dominio `.up.railway.app` por defecto:

```
https://myapp-production.up.railway.app
```

### Dominio Personalizado

```bash
# Settings → Networking → Add Domain
# Ingresa tu dominio (e.g., myapp.com)

# DNS Configuration:
# Type: CNAME
# Name: www (o @)
# Value: custom.up.railway.app
```

SSL es automático y gratuito.

---

## 📊 Health Checks

```toml
[deploy]
healthcheckPath = "/health"
healthcheckPort = 3000
restartPolicyType = "OnFailure"
restartPolicyAmount = 10
```

```typescript
// Endpoint de health check
app.get('/health', (req, res) => {
  res.status(200).json({ status: 'ok' });
});
```

---

## 🔍 Logs y Monitoring

### Ver Logs

```bash
# Logs en tiempo real
railway logs

# Logs de un deployment específico
railway logs <deployment-id>

# Guardar logs
railway logs > deployment.log
```

### Métricas

Dashboard muestra:
- CPU usage
- Memory usage
- Request count
- Response times

---

## 🚨 Common Issues

### Issue 1: Build Fallidos

```bash
# Ver build logs detallados
railway logs --type build

# Verificar que el comando de build existe
railway run npm run build
```

### Issue 2: Variables de Entorno

```bash
# Asegúrate que las variables están configuradas
railway variables

# Para exponer en cliente (Next.js)
railway variables set NEXT_PUBLIC_API_URL=https://api.example.com
```

### Issue 3: Timeout en Startup

```toml
[deploy]
startupTimeout = 60
```

---

## 💰 Pricing

| Plan | Precio | Features |
|------|--------|----------|
| **Hobby** | Gratis | $5 créditos/mes, 1 proyecto, 500MB RAM |
| **Pro** | $20/mes | $40 créditos, ilimitados proyectos, 8GB RAM |
| **Team** | $40/mes | $80 créditos, colaboración, 16GB RAM |

Los créditos se usan según consumo real:
- CPU: ~$0.001/vCPU-second
- RAM: ~$0.0001/GB-second
- Traffic: ~$0.10/GB egress

---

## 🔗 Comparativa Rápida

| Feature | Railway | Vercel | Netlify |
|---------|---------|--------|---------|
| Databases integradas | ✅ | ❌ | ❌ |
| Docker | ✅ | ❌ | ❌ |
| Multi-región | Limitado | ✅ | ✅ |
| Pricing | Pay-as-you-go | Fijo | Fijo |
| Starter free | $5 créditos | 100GB/mes | 100GB/mes |

---

## 🔗 Enlaces Relacionados

- [[Vercel.md|Vercel]]
- [[Netlify.md|Netlify]]
- [[PostgreSQL-Supabase.md|PostgreSQL & Supabase]]
- [[Redis-Caching.md|Redis]]
- [[MongoDB.md|MongoDB]]
- [[../../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con setup, CLI, Docker y ejemplos |
