---
title: Vercel Skills
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [skills, vercel, mcp, opencode, deployment]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["01 Tecnolog\u00edas/Herramientas/IA/Claude Code", "01 Tecnolog\u00edas/Herramientas/IA/OpenCode", "01 Tecnolog\u00edas/Herramientas/IA/Configuraci\u00f3n Ejemplo", "01 Tecnolog\u00edas/Herramientas/IA/MCP", "01 Tecnolog\u00edas/Herramientas/IA/Skills/Anthropic"]
migrated: true
migratedFrom: 01 Stack/IA-Skills/Vercel-MCP.md
---

# 🚀 Vercel Skills

Suite de skills para integración con Vercel, permitiendo deploy automático, análisis de proyectos y gestión de deployments.

---

## 📖 Descripción General

Los **Vercel Skills** permiten:
- Desplegar proyectos automáticamente desde IA
- Analizar logs y errores de deployment
- Gestionar variables de entorno
- Configurar dominios y DNS
- Monitorizar performance y analytics
- Controlar builds y rollbacks

---

## 🎯 Capacidades

| Capacidad | Descripción |
|-----------|------------|
| **Deploy** | Despliegue automático de proyectos |
| **Project Management** | Crear y gestionar proyectos |
| **Environment Variables** | Configurar variables seguras |
| **Domains & DNS** | Gestión de dominios |
| **Analytics** | Datos de rendimiento y tráfico |
| **Build Control** | Configurar builds y prerenders |
| **Logs Access** | Ver logs de deployment |
| **Rollback** | Revertir a versiones previas |

---

## 💻 Instalación

### OpenCode

```bash
# Instalación básica
opencode skills install vercel

# Instalación con actualizaciones
opencode skills install vercel --latest

# Verificar instalación
opencode skills info vercel
```

### Claude Code

```bash
# Configuración en mcp.json
claude mcp install vercel

# O manualmente en mcp.json
{
  "mcpServers": {
    "vercel": {
      "command": "npx",
      "args": ["@vercel/mcp-server"],
      "env": {
        "VERCEL_TOKEN": "YOUR_VERCEL_TOKEN"
      }
    }
  }
}
```

### npm/Node.js

```bash
npm install @vercel/mcp-server
npm install vercel

# Verificar
npm list @vercel/mcp-server
```

---

## 🔑 Configuración Requerida

### Token de Vercel

```bash
# 1. Ir a: https://vercel.com/account/tokens
# 2. Crear nuevo token
# 3. Guardar en variable de entorno

export VERCEL_TOKEN="YOUR_VERCEL_TOKEN"
export VERCEL_ORG_ID="YOUR_ORG_ID"  # Opcional
```

### config.json

```json
{
  "skills": {
    "vercel": {
      "enabled": true,
      "token": "${VERCEL_TOKEN}",
      "org_id": "${VERCEL_ORG_ID}",
      "auto_deploy": false,
      "build_timeout": 600,
      "environment": "production"
    }
  }
}
```

### CLI Local

```bash
# Instalar CLI de Vercel
npm install -g vercel

# Autenticarse
vercel login

# Verificar
vercel whoami
```

---

## 📚 Casos de Uso

### 1. Deploy Automático

```bash
opencode "deploy my Next.js project to Vercel"
```
Detecta automáticamente la estructura y configura el deployment.

### 2. Análisis de Performance

```bash
opencode "analyze my Vercel project performance metrics"
```
Extrae datos de Core Web Vitals, velocidad, etc.

### 3. Gestión de Errores

```bash
opencode "check my deployment logs and find errors"
```
Lee logs y proporciona análisis de problemas.

### 4. Configuración de Variables

```bash
opencode "add environment variables to my Vercel project"
```
Configura variables seguras automáticamente.

---

## 🛠️ Comandos Disponibles

### Modo Interactivo

```bash
opencode

# En sesión:
@vercel deploy
@vercel status
@vercel logs
@vercel env list
```

### Modo Directo

```bash
# Deploy
opencode "deploy this project to vercel"

# Status
opencode "show vercel deployment status"

# Logs
opencode "get vercel build logs"

# Environment
opencode "list vercel environment variables"
```

### Con Claude Code

```bash
claude "deploy using vercel skills"
claude "analyze vercel analytics"
```

---

## 📊 Proyectos Soportados

| Framework | Soporte | Build | Preview |
|-----------|---------|-------|---------|
| **Next.js** | ✅ Nativo | Auto | Automático |
| **React** | ✅ Completo | Vite/CRA | Automático |
| **Vue.js** | ✅ Completo | Nuxt/Vite | Automático |
| **Svelte** | ✅ Completo | SvelteKit | Automático |
| **Static** | ✅ HTML/CSS | N/A | Directo |
| **Node.js** | ✅ Serverless | npm build | API Routes |
| **Python** | ✅ Serverless | pip install | API Routes |

---

## 🔬 Ejemplos Prácticos

### Ejemplo 1: Deploy Automático Next.js

```bash
opencode "deploy my next.js app at ./my-app to vercel"
```

**Pasos automáticos:**
1. Detecta Next.js
2. Configura build (`npm run build`)
3. Despliega en Vercel
4. Genera URL preview
5. Configura dominio (si existe)

### Ejemplo 2: Análisis de Performance

```bash
opencode "get web vitals for my vercel deployment"
```

**Datos retornados:**
- Largest Contentful Paint (LCP)
- First Input Delay (FID)
- Cumulative Layout Shift (CLS)
- Métricas de rendimiento

### Ejemplo 3: Configurar Variables de Entorno

```bash
opencode "set DATABASE_URL and API_KEY in vercel"
```

**Resultado:**
- Variables configuradas de forma segura
- Disponibles en environment production
- Redeploy automático (opcional)

### Ejemplo 4: Rollback a Versión Anterior

```bash
opencode "rollback my vercel deployment to the previous version"
```

**Acción:**
- Identifica último deployment exitoso
- Revierte automáticamente
- Actualiza alias

---

## 🔐 Seguridad

### Variables Sensibles

```bash
# NUNCA incluir en commits
export VERCEL_TOKEN="..." # Solo local
export DATABASE_URL="..." # En Vercel dashboard

# Usar .env.local en desarrollo
cat > .env.local << EOF
DATABASE_URL=postgresql://...
API_KEY=sk-...
EOF
```

### Buenas Prácticas

1. **Tokens**: Usar tokens con permisos limitados
2. **Secrets**: Guardar en Vercel dashboard, no en código
3. **Deploy Preview**: Usar para testing antes de prod
4. **Protección de Rama**: Requiere aprobación para main

---

## ⚠️ Límites y Cuotas

| Límite | Hobby | Pro |
|--------|-------|-----|
| Deployments/mes | 100 | Ilimitados |
| Serverless Functions | 12 máximo | 24 máximo |
| Banda ancha | 100GB | Ilimitada |
| Duración build | 45 min | 45 min |
| Preview URLs | 24h | 7 días |

---

## 🐛 Troubleshooting

### Error: "Token invalid"

```bash
# Verificar token
echo $VERCEL_TOKEN

# Obtener nuevo token en:
# https://vercel.com/account/tokens

# Re-configurar
export VERCEL_TOKEN="vercel_..."
```

### Build falla

```bash
# Ver logs detallados
opencode "show vercel build logs"

# Verificar config en vercel.json
cat vercel.json

# Reinstalar dependencias
rm -rf node_modules package-lock.json
npm install
```

### Variables de entorno no funcionan

```bash
# Listar variables configuradas
opencode "list vercel environment variables"

# Verificar en código
console.log(process.env.MY_VAR)

# Redeploy para aplicar
opencode "redeploy vercel with environment variables"
```

---

## 📊 Monitorización

### Analytics

```bash
opencode "get vercel analytics for my project"
```

**Métricas incluidas:**
- Requests totales
- Errores y status 5xx
- Latencia promedio
- Uso de CPU y memoria

### Logs en Tiempo Real

```bash
opencode "stream vercel logs for my deployment"
```

---

## 🔗 Referencias

- **Documentación oficial**: [vercel.com/docs](https://vercel.com/docs)
- **Dashboard**: [vercel.com/dashboard](https://vercel.com/dashboard)
- **CLI**: [vercel.com/cli](https://vercel.com/cli)
- **MCP Server**: [github.com/vercel/mcp-server](https://github.com/vercel/mcp-server)
- **API Reference**: [vercel.com/docs/api](https://vercel.com/docs/api)

---

## 🔗 Referencias Relacionadas

- [[01 Tecnologías/Herramientas/IA/Claude Code]] - Integración con Claude Code
- [[01 Tecnologías/Herramientas/IA/OpenCode]] - Integración con OpenCode
- [[01 Tecnologías/Herramientas/IA/Skills/Anthropic]] - Skills de Anthropic
- [[01 Tecnologías/Herramientas/IA/MCP]] - Model Context Protocol
- [[01 Tecnologías/Herramientas/IA/Configuración Ejemplo]] - Ejemplos de configuración

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial con documentación completa |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
