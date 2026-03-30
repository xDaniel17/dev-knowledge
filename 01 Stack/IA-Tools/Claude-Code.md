---
title: Claude Code
date: 2025-01-01
lastUpdated: 2026-03-29
tags: [cli, ia, anthropic, desarrollo, herramientas]
category: 01 Stack
author: Daniel
version: 2.0.0
status: active
related: ["Skills/Anthropic", "Configuraci\u00f3n Ejemplo", "01 Tecnolog\u00edas/Herramientas/IA/README", "MCP", "OpenCode"]
migrated: true
migratedFrom: 01 Stack/IA-Tools/Claude-Code.md
---

# 🔧 Claude Code

CLI profesional de Anthropic para desarrollo de software asistido por IA con capacidades avanzadas de análisis, generación y refactoring de código.

- **Web**: [anthropic.com/claude-code](https://www.anthropic.com/claude-code)
- **Docs**: [docs.anthropic.com](https://docs.anthropic.com)
- **Repositorio**: [github.com/anthropics](https://github.com/anthropics)
- **Soporte**: [support.anthropic.com](https://support.anthropic.com)

---

## ✨ Características Principales

| Característica | Descripción | Caso de Uso |
|---|---|---|
| **Análisis de Código** | Revisa, explica y mejora código existente | Auditoría de calidad |
| **Generación de Código** | Escribe código desde descripción en lenguaje natural | Desarrollo rápido |
| **Refactoring** | Mejora estructura y legibilidad | Mantenimiento |
| **Debuggingasistido** | Identifica y propone soluciones de bugs | Resolución de problemas |
| **Documentation** | Genera documentación automática | Documentar proyectos |
| **Testing** | Propone y genera test cases | QA automation |
| **Project Context** | Lee archivos del proyecto completo | Análisis holístico |
| **Multiple Languages** | Soporta 50+ lenguajes de programación | Polyglot development |

---

## 📦 Instalación Completa

### Prerequisitos
- Node.js 18+ o npm 9+
- API key de Anthropic válida
- Sistema operativo: macOS, Linux o Windows

### macOS

```bash
# Opción 1: Homebrew (Recomendado)
brew install anthropic-cli

# Opción 2: npm global
npm install -g @anthropic-ai/claude-cli

# Verificar instalación
claude --version
```

### Linux

```bash
# Ubuntu/Debian
sudo apt-get update
sudo apt-get install nodejs npm

npm install -g @anthropic-ai/claude-cli

# O descargar binario
curl -L https://github.com/anthropics/anthropic-cli/releases/download/latest/anthropic-cli-linux-x86_64 -o /usr/local/bin/claude
chmod +x /usr/local/bin/claude
```

### Windows

```powershell
# Opción 1: npm
npm install -g @anthropic-ai/claude-cli

# Opción 2: Descargar installer
# Ir a: https://github.com/anthropics/anthropic-cli/releases
# Descargar anthropic-cli-windows-x86_64.exe
# Ejecutar el instalador

# Verificar
claude --version
```

---

## 🔑 Autenticación y Configuración

### Obtener API Key

1. Ir a [console.anthropic.com](https://console.anthropic.com)
2. Crear cuenta o iniciar sesión
3. Navegar a "API Keys"
4. Hacer clic en "Create new API key"
5. Copiar la clave (formato: `sk-ant-...`)

### Configurar Autenticación

```bash
# Método 1: Variable de entorno
export ANTHROPIC_API_KEY="sk-ant-YOUR_API_KEY_HERE"

# Método 2: Archivo de configuración
claude auth login
# (Sigue las instrucciones interactivas)

# Método 3: Archivo .env
# En el directorio del proyecto:
echo "ANTHROPIC_API_KEY=sk-ant-YOUR_KEY" > .env

# Verificar autenticación
claude auth status
```

### Ubicación de Configuración

| SO | Ruta |
|----|------|
| **macOS** | `~/.claude/config.json` |
| **Linux** | `~/.config/claude/config.json` |
| **Windows** | `%APPDATA%\Claude\config.json` |

---

## 💻 Comandos Principales

### 1. Modo Interactivo

```bash
claude
```

**Descripción:** Abre una sesión interactiva donde puedes:
- Escribir prompts continuamente
- Mantener contexto entre preguntas
- Refinar respuestas iterativamente

**Ejemplo:**
```bash
$ claude
claude> Crea una función en JavaScript que valide emails
# [Claude responde con código]
claude> Hazla async
# [Claude mejora la función anterior]
claude> exit
```

---

### 2. Ejecutar Prompt Único

```bash
claude "tu prompt aquí"
```

**Descripción:** Ejecuta un prompt único y retorna el resultado

**Ejemplos:**
```bash
# Explicar código
claude "Explica qué hace este código: const x = arr.filter(n => n > 5)"

# Generar código
claude "Crea una función recursiva que calcula factorial en Python"

# Análisis
claude "¿Cuáles son las vulnerabilidades de seguridad en este código?"
```

---

### 3. Modo Proyecto (-p o --project)

```bash
claude -p
# o
claude --project
```

**Descripción:** Analiza el proyecto actual, leyendo estructura de archivos y contenido

**Casos de uso:**
```bash
# Entender arquitectura del proyecto
claude -p "¿Cuál es la arquitectura general de este proyecto?"

# Análisis de dependencias
claude -p "¿Qué dependencias son críticas y cuáles son opcionales?"

# Refactoring global
claude -p "Sugiere refactoring para mejorar mantenibilidad"

# Testing
claude -p "Crea un plan de testing comprehensive para este proyecto"
```

---

### 4. Modo Proyecto con Prompt

```bash
claude -p "tu prompt especificando qué quieres que analice del proyecto"
```

**Ejemplo real:**
```bash
cd my-react-app/
claude -p "Revisa este proyecto React y sugiere optimizaciones de performance"
```

---

### 5. Especificar Lenguaje

```bash
claude --language python "escribe una función que..."
claude --language typescript "crea una interfaz para..."
```

---

### 6. Ver Ayuda

```bash
claude --help
claude <comando> --help

# Ejemplos:
claude auth --help
claude -p --help
```

---

## ⚙️ Configuración Avanzada

### config.json Completa

**Ubicación:** `~/.claude/config.json`

```json
{
  "api_key": "sk-ant-YOUR_API_KEY",
  "model": "claude-3-5-sonnet-20241022",
  "temperature": 0.7,
  "max_tokens": 4096,
  "timeout": 30,
  "enable_mcp": true,
  "mcp_servers": [
    {
      "name": "anthropic",
      "command": "npx",
      "args": ["@anthropic-ai/anthropic-mcp"]
    },
    {
      "name": "vercel",
      "command": "npx",
      "args": ["@vercel/mcp-server"]
    }
  ],
  "logging": {
    "level": "info",
    "file": "~/.claude/logs/claude.log"
  },
  "features": {
    "streaming": true,
    "syntax_highlighting": true,
    "code_execution": false
  }
}
```

### Parámetros Clave

| Parámetro | Rango | Descripción |
|-----------|-------|------------|
| **temperature** | 0.0 - 2.0 | Creatividad (0=determinístico, 2=muy creativo) |
| **max_tokens** | 1 - 4096 | Límite de tokens en respuesta |
| **timeout** | 10 - 300 | Segundos antes de timeout |
| **model** | - | Modelo a usar (recomendado: claude-3-5-sonnet) |

---

## 🔌 Integración en Editores

### VS Code

**1. Instalar extensión:**
- Abrir VS Code
- Ir a Extensions (Cmd+Shift+X)
- Buscar "Claude Code"
- Instalar extensión oficial de Anthropic

**2. Configurar:**
```json
// settings.json
{
  "claude.apiKey": "${ANTHROPIC_API_KEY}",
  "claude.model": "claude-3-5-sonnet-20241022",
  "claude.temperature": 0.7
}
```

**3. Usar:**
- Cmd+K: Abrir Claude Code
- Escribe tu prompt
- Ctrl+Enter: Ejecutar
- Verás el código sugerido directamente en el editor

---

### Cursor Editor

```bash
# Cursor tiene integración nativa
# Simplemente activa con Cmd+K y comienza a escribir

# En Cursor settings:
# Claude API: sk-ant-YOUR_KEY
```

**Características en Cursor:**
- Edición en vivo del código
- Sugerencias inline
- Refactoring visual
- Chat con contexto

---

### JetBrains IDEs (IntelliJ, PyCharm, WebStorm)

**1. Instalar plugin:**
- Ir a Plugins
- Buscar "Claude Code"
- Instalar

**2. Configurar:**
```
IDE Settings > Tools > Claude Code
- API Key: sk-ant-YOUR_KEY
- Model: claude-3-5-sonnet-20241022
```

**3. Usar:**
- Click derecho en código → "Ask Claude"
- O usar Alt+A para abrir chat

---

## 🌐 MCP (Model Context Protocol)

Claude Code soporta MCP servers para extender funcionalidades.

### Servidores MCP Disponibles

| Servidor | Propósito | Instalación |
|----------|----------|------------|
| **Figma** | Integración con diseños | `npm install -g @figma/mcp-server` |
| **GitHub** | Acceso a repositorios | `npm install -g @octokit/mcp-server` |
| **Stripe** | API de pagos | `npm install -g @stripe/mcp-server` |
| **Vercel** | Deploy automático | `npm install -g @vercel/mcp-server` |

### Configurar MCP Server

```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["@figma/mcp-server"],
      "env": {
        "FIGMA_API_KEY": "your-figma-token"
      }
    },
    "github": {
      "command": "npx",
      "args": ["@octokit/mcp-server"],
      "env": {
        "GITHUB_TOKEN": "your-github-token"
      }
    }
  }
}
```

---

## 📚 Ejemplos de Uso Práctico

### Ejemplo 1: Generar Función TypeScript

```bash
claude "Crea una función TypeScript para validar contraseñas con estos criterios:
- Mínimo 8 caracteres
- Al menos 1 mayúscula
- Al menos 1 número
- Al menos 1 carácter especial"
```

**Respuesta esperada:**
```typescript
function validatePassword(password: string): boolean {
  const regex = /^(?=.*[A-Z])(?=.*\d)(?=.*[!@#$%^&*]).{8,}$/;
  return regex.test(password);
}
```

---

### Ejemplo 2: Explicar Código Complejo

```bash
claude -p "Este proyecto React es bastante complejo. 
Explica el flujo de datos principal y sugiere 
dónde podrían haber problemas de performance"
```

---

### Ejemplo 3: Generar Tests

```bash
claude "Crea tests unitarios con Jest para esta función:

function factorial(n: number): number {
  return n <= 1 ? 1 : n * factorial(n - 1);
}"
```

---

### Ejemplo 4: Refactoring

```bash
claude "Refactoriza este código JavaScript para mejorar
legibilidad y performance:

function getData() {
  let data = [];
  for (let i = 0; i < items.length; i++) {
    if (items[i].active) {
      data.push(items[i].name);
    }
  }
  return data;
}"
```

---

### Ejemplo 5: Documentación Automática

```bash
claude -p "Genera documentación completa (JSDoc) para
todas las funciones en este proyecto"
```

---

## 🎯 Mejores Prácticas

| Práctica | Descripción | Ejemplo |
|----------|-------------|---------|
| **Prompts específicos** | Sé claro sobre qué quieres | ❌ "Crea una función" → ✅ "Crea una función async que fetche datos de API con retry" |
| **Contexto completo** | Proporciona contexto necesario | Incluye requisitos, restricciones, tecnologías |
| **Modo proyecto** | Úsalo para análisis global | `claude -p "Revisa seguridad del proyecto"` |
| **Iteración** | Refina respuestas en modo interactivo | `claude` → refina con prompts adicionales |
| **Lenguaje específico** | Menciona lenguaje si no es obvio | "En Python usando async/await..." |
| **Ejemplos** | Muestra ejemplos de input/output | Facilita precisión |

---

## ⚡ Optimizaciones

### Reducir Tiempo de Respuesta

```bash
# Usar temperatura más baja (más rápido, menos creativo)
claude --temperature 0.3 "prompt"

# Limitar tokens
claude --max-tokens 512 "prompt corto"
```

### Manejo de Proyectos Grandes

```bash
# Especificar archivos relevantes
cd my-project
claude -p "Solo revisa los archivos en src/components"

# O en modo interactivo
claude
> Analiza solo el archivo main.ts
```

---

## 🐛 Troubleshooting

### Error: "API Key not found"

```bash
# Verificar variable de entorno
echo $ANTHROPIC_API_KEY

# Si está vacío:
export ANTHROPIC_API_KEY="sk-ant-YOUR_KEY"

# O configurar de forma persistente
echo 'export ANTHROPIC_API_KEY="sk-ant-YOUR_KEY"' >> ~/.zshrc
source ~/.zshrc
```

---

### Error: "Model not available"

```bash
# Verificar modelos disponibles
claude --help | grep model

# Usar modelo por defecto (recomendado)
# O configurar en ~/.claude/config.json:
# "model": "claude-3-5-sonnet-20241022"
```

---

### Error: "Timeout"

```bash
# Aumentar timeout en config.json
{
  "timeout": 60  // segundos
}

# O por comando
claude --timeout 60 "prompt"
```

---

### Error: "Port already in use" (VS Code/Cursor)

```bash
# Cerrar otras instancias
pkill -f claude

# O cambiar puerto en config
```

---

## 📊 Modelos Disponibles

| Modelo | Contexto | Entrada | Salida | Ideal Para |
|--------|----------|---------|--------|-----------|
| **Claude 3.5 Sonnet** | 200K | $3/1M | $15/1M | Propósito general, mejor balance |
| **Claude 3 Opus** | 200K | $15/1M | $75/1M | Tareas muy complejas |
| **Claude 3 Haiku** | 200K | $0.80/1M | $4/1M | Baja latencia, tareas simples |

**Recomendación:** Usar `claude-3-5-sonnet-20241022` para la mayoría de casos.

---

## 🚀 Casos de Uso Avanzados

### 1. Auditoría de Seguridad

```bash
claude -p "Realiza una auditoría de seguridad completa
del código. Identifica vulnerabilidades OWASP,
problemas de validación, y riesgos de inyección"
```

---

### 2. Migración de Codebase

```bash
claude -p "Crea un plan detallado para migrar este 
proyecto de JavaScript a TypeScript. 
Incluye pasos, herramientas y timeline"
```

---

### 3. Performance Analysis

```bash
claude -p "Analiza el performance de este código.
Identifica cuellos de botella, optimizaciones
posibles y complejidad algorítmica"
```

---

### 4. Generación de API

```bash
claude "Diseña una REST API para un e-commerce.
Incluye endpoints, modelos de datos, 
códigos de error y ejemplos de requests"
```

---

## 🔗 Referencias Relacionadas

- [[Configuración Ejemplo]] - Ejemplos de configuración
- [[OpenCode]] - CLI alternativo
- [[MCP]] - Model Context Protocol
- [[Skills/Anthropic]] - Anthropic MCP skills
- [[01 Tecnologías/Herramientas/IA/README]] - Índice de herramientas IA

---

## 📚 Recursos Oficiales

- [Documentación Oficial](https://docs.anthropic.com)
- [Console de API](https://console.anthropic.com)
- [GitHub Anthropic](https://github.com/anthropics)
- [Pricing](https://www.anthropic.com/pricing)
- [Status Page](https://status.anthropic.com)

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-29 | Daniel Herrera | Documentación completa mejorada con ejemplos, MCP, troubleshooting |
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
