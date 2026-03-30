---
title: Anthropic Skills
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [skills, anthropic, mcp, opencode, ia]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["01 Tecnolog\u00edas/Herramientas/IA/Claude Code", "01 Tecnolog\u00edas/Herramientas/IA/OpenCode", "01 Tecnolog\u00edas/Herramientas/IA/Skills/Vercel", "01 Tecnolog\u00edas/Herramientas/IA/Configuraci\u00f3n Ejemplo", "01 Tecnolog\u00edas/Herramientas/IA/MCP"]
migrated: true
migratedFrom: 01 Stack/IA-Skills/Anthropic-MCP.md
---

# 🤖 Anthropic Skills

Suite de skills para integración profunda con Claude y modelos de Anthropic en OpenCode y Claude Code.

---

## 📖 Descripción General

Los **Anthropic Skills** permiten:
- Acceso directo a modelos de Anthropic (Claude 3.5 Sonnet, Opus, Haiku)
- Manejo de tokens y contexto avanzado
- Batch processing de prompts
- Vision API para análisis de imágenes
- Files API para documentos largos

---

## 🎯 Capacidades

| Capacidad | Descripción |
|-----------|------------|
| **Model Access** | Acceso a todos los modelos de Anthropic |
| **Token Management** | Control precisodel uso de tokens |
| **Vision API** | Análisis de imágenes y PDFs |
| **Files API** | Procesamiento de documentos grandes |
| **Batch API** | Procesamiento en lote de requests |
| **Streaming** | Respuestas en tiempo real |
| **Tool Use** | Uso de funciones personalizadas |

---

## 💻 Instalación

### OpenCode

```bash
# Instalación básica
opencode skills install anthropic

# Instalación con actualizaciones
opencode skills install anthropic --latest

# Verificar instalación
opencode skills info anthropic
```

### Claude Code

```bash
# Configuración en mcp.json
claude mcp install anthropic

# O manualmente en mcp.json
{
  "mcpServers": {
    "anthropic": {
      "command": "npx",
      "args": ["@anthropic-ai/anthropic-mcp"],
      "env": {
        "ANTHROPIC_API_KEY": "sk-ant-YOUR_API_KEY"
      }
    }
  }
}
```

### npm/Node.js

```bash
npm install @anthropic-ai/anthropic-mcp
npm install @anthropic-ai/sdk

# Verificar
npm list @anthropic-ai/anthropic-mcp
```

---

## 🔑 Configuración Requerida

### API Key

```bash
# Obtener en: https://console.anthropic.com
# Guardar en variable de entorno

export ANTHROPIC_API_KEY="sk-ant-YOUR_API_KEY_HERE"
```

### config.json

```json
{
  "skills": {
    "anthropic": {
      "enabled": true,
      "api_key": "${ANTHROPIC_API_KEY}",
      "default_model": "claude-3-5-sonnet-20241022",
      "max_tokens": 4096,
      "temperature": 0.7,
      "timeout": 30
    }
  }
}
```

---

## 📚 Casos de Uso

### 1. Análisis de Código

```bash
opencode "analyze this code for performance issues using anthropic"
```
Utiliza visión para revisar capturas de pantalla o imágenes de código.

### 2. Procesamiento de Documentos

```bash
opencode "read and summarize this PDF document using anthropic"
```
Acceso a Files API para documentos grandes (hasta 20MB).

### 3. Análisis de Imágenes

```bash
opencode "describe the content of this image in detail"
```
Vision API integrado para análisis de PNG, JPEG, GIF, WebP.

### 4. Batch Processing

```bash
opencode "process 100 analysis requests in batch using anthropic"
```
Procesa múltiples requests simultáneamente con Batch API.

---

## 🛠️ Comandos Disponibles

### Modo Interactivo

```bash
opencode

# En sesión:
@anthropic analyze this code
@anthropic vision analyze image.png
@anthropic batch process data.json
```

### Modo Directo

```bash
opencode "use anthropic to analyze this"
opencode "use anthropic vision for image analysis"
opencode "create batch job with anthropic"
```

### Con Claude Code

```bash
claude "use anthropic skills to analyze"
```

---

## 📊 Modelos Disponibles

| Modelo | Contexto | Entrada | Salida | Mejor Para |
|--------|----------|---------|--------|-----------|
| **Claude 3.5 Sonnet** | 200K | $3/1M | $15/1M | Uso general, análisis |
| **Claude 3 Opus** | 200K | $15/1M | $75/1M | Tareas complejas |
| **Claude 3 Haiku** | 200K | $0.80/1M | $4/1M | Baja latencia |

---

## 🔬 Ejemplos Prácticos

### Ejemplo 1: Análisis de Código con Vision

```bash
opencode "analyze this screenshot for code quality"
```

**Respuesta esperada:**
- Identificación de problemas de rendimiento
- Sugerencias de refactoring
- Explicación de patrones detectados

### Ejemplo 2: Procesamiento de Documento

```bash
opencode "read this 50-page PDF and extract key metrics"
```

**Respuesta esperada:**
- Extracción automática de datos
- Resumen estructurado
- Tablas con información relevante

### Ejemplo 3: Batch Job

```bash
opencode "create a batch job to classify 1000 emails"
```

**Respuesta esperada:**
- Job ID para tracking
- Estimación de costo
- Tiempo de procesamiento

---

## ⚠️ Límites y Cuotas

| Límite | Valor |
|--------|-------|
| Tokens de entrada | 4,096 (configurable) |
| Tokens de salida | 4,096 (configurable) |
| Tamaño de imagen | 5MB máximo |
| Tamaño de documento | 20MB máximo |
| Batch requests | 100,000 máximo |
| Rate limit | Según plan |

---

## 🐛 Troubleshooting

### Error: "API Key not found"

```bash
# Verificar
echo $ANTHROPIC_API_KEY

# Si está vacío, configurar
export ANTHROPIC_API_KEY="sk-ant-YOUR_KEY"

# Verificar en OpenCode
opencode skills info anthropic
```

### Error: "Model not available"

```bash
# Verificar modelos disponibles
opencode "list available anthropic models"

# Actualizar configuración con modelo válido
# En config.json cambiar "claude-3-5-sonnet-20241022"
```

### Vision API no funciona

```bash
# Asegurar que la imagen está en formato soportado
# PNG, JPEG, GIF, WebP

# Ejemplo correcto:
opencode "analyze image.png using vision"
```

---

## 🔗 Referencias

- **Documentación oficial**: [docs.anthropic.com](https://docs.anthropic.com)
- **Console**: [console.anthropic.com](https://console.anthropic.com)
- **SDK**: [github.com/anthropics/anthropic-sdk-python](https://github.com/anthropics/anthropic-sdk-python)
- **MCP Server**: [github.com/anthropics/anthropic-mcp](https://github.com/anthropics/anthropic-mcp)

---

## 🔗 Referencias Relacionadas

- [[01 Tecnologías/Herramientas/IA/Claude Code]] - Integración con Claude Code
- [[01 Tecnologías/Herramientas/IA/OpenCode]] - Integración con OpenCode
- [[01 Tecnologías/Herramientas/IA/Skills/Vercel]] - Skills de Vercel
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
