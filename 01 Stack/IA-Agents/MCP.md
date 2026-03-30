---
title: MCP (Model Context Protocol)
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["02-Herramientas-Desarrollo", "Agentes-IA", "Skills-Ecosystem"]
migrated: true
migratedFrom: 01 Stack/IA-Agents/MCP.md
---

# MCP (Model Context Protocol)

**MCP** es un protocolo abierto que permite conectar **agentes de IA** con herramientas, servicios externos y contexto del proyecto.

## 🎯 ¿Qué es MCP?

MCP es un estándar para que los agentes de IA accedan a:
- **Herramientas externas** (Figma, GitHub, etc.)
- **Datos del proyecto** (código, archivos, bases de datos)
- **APIs de terceros** (Stripe, Supabase, etc.)
- **Servicios especializados** (búsqueda, análisis, etc.)

Sin MCP, el agente solo puede ver el código que le pases. Con MCP, accede a todo tu contexto.

## 🏗️ Arquitectura

```
┌─────────────────────────────────────────────────┐
│         Agente de IA (Claude Code, etc)         │
├─────────────────────────────────────────────────┤
│          MCP Client Protocol                     │
├─────────────────────────────────────────────────┤
│                  MCP Servers                     │
│  ┌──────────┬──────────┬──────────┬──────────┐ │
│  │  Figma   │ GitHub   │ Supabase │ Stripe   │ │
│  └──────────┴──────────┴──────────┴──────────┘ │
└─────────────────────────────────────────────────┘
```

## 📦 Cómo funciona

### 1. Instalación de un MCP Server

```bash
# Ejemplo: instalar MCP de Figma
npm install @figma/mcp

# O por URL
mcp install https://github.com/figma/figma-mcp
```

### 2. Configuración

MCP se configura en archivos JSON específicos de cada herramienta:

**VS Code + Claude Code:**
```
~/Library/Application Support/Code/User/mcp.json
```

**Cursor:**
```
~/Library/Application Support/Cursor/User/mcp.json
```

**OpenCode:**
```
~/.opencode/mcp.json
```

### 3. Formato de configuración

```json
{
  "mcpServers": {
    "figma": {
      "command": "node",
      "args": ["./node_modules/@figma/mcp/dist/index.js"],
      "env": {
        "FIGMA_API_KEY": "your-api-key"
      }
    },
    "github": {
      "command": "npx",
      "args": ["@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "your-token"
      }
    }
  }
}
```

## 🔌 MCP Servers populares

### Oficiales (Anthropic)

| Servidor | Descripción | Autenticación |
|----------|-------------|---------------|
| **Figma** | Diseño, prototipos | API Key |
| **GitHub** | Repositorios, PRs, issues | OAuth/Token |
| **Google Drive** | Documentos, hojas de cálculo | OAuth |
| **Slack** | Mensajes, canales | Webhook/Token |
| **Fetch** | HTTP requests genéricos | N/A |
| **SQLite** | Base de datos local | N/A |

### Community (Terceros)

- **Supabase** - Base de datos PostgreSQL
- **Stripe** - Pagos y facturación
- **AWS** - Servicios de Amazon
- **Linear** - Gestión de tareas
- **Notion** - Base de datos personal
- **Airtable** - Hojas de cálculo
- **GitLab** - Alternativa a GitHub

## 🛠️ Mi configuración actual

### Figma MCP (VS Code)

```json
{
  "servers": {
    "com.figma.mcp/mcp": {
      "type": "http",
      "url": "https://mcp.figma.com/mcp",
      "version": "1.0.3"
    }
  }
}
```

**Permite:**
- Acceder a archivos de Figma
- Ver prototipos
- Extraer componentes
- Analizar diseños

## 📝 Creando un MCP Server personalizado

Si necesitas un servidor MCP personalizado:

```javascript
// my-mcp-server.js
import { Server } from "@modelcontextprotocol/sdk/server/index.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";

const server = new Server({
  name: "my-custom-server",
  version: "1.0.0",
});

// Definir herramientas disponibles
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "query_database",
        description: "Query custom database",
        inputSchema: {
          type: "object",
          properties: {
            query: { type: "string" }
          }
        }
      }
    ]
  };
});

// Manejar llamadas a herramientas
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "query_database") {
    // Ejecutar lógica
    return { content: [{ type: "text", text: results }] };
  }
});

const transport = new StdioServerTransport();
await server.connect(transport);
```

## 🔐 Seguridad y mejores prácticas

### Variables de entorno
```bash
# Nunca hardcodear credenciales
# Usar variables de entorno
export FIGMA_API_KEY="abc123..."
export GITHUB_TOKEN="ghp_..."
```

### Permisos granulares
```json
{
  "permissions": {
    "figma": ["read"],
    "github": ["read", "write:pull-requests"],
    "slack": ["send_messages"]
  }
}
```

### Validar inputs
```javascript
// Validar datos antes de usar
if (!isValidQuery(input)) {
  throw new Error("Invalid input");
}
```

## 🚨 Troubleshooting

### "MCP Server not found"
```bash
# Verificar que está instalado
npm list @figma/mcp

# Verificar path en configuración
cat ~/.opencode/mcp.json
```

### "Authentication failed"
```bash
# Verificar credenciales
echo $FIGMA_API_KEY
echo $GITHUB_TOKEN

# Re-generar token si es necesario
```

### "Server timeout"
```bash
# El servidor tardó demasiado
# Aumentar timeout en configuración
"timeout": 30000 // 30 segundos
```

## 🔗 Referencias relacionadas

- [[Agentes-IA]] - Cómo usan los agentes MCP
- [[Skills-Ecosystem]] - Skills como alternativa a MCP
- [[02-Herramientas-Desarrollo]] - Herramientas que soportan MCP

## 📚 Recursos externos

- [MCP Official Docs](https://modelcontextprotocol.io)
- [MCP GitHub Servers](https://github.com/modelcontextprotocol/servers)
- [Awesome MCP](https://github.com/punkpeye/awesome-mcp-servers)

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documentación inicial: protocolo, servers, configuración |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
