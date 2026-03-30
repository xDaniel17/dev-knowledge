---
title: IA Skills - Extensiones y Automatización
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - ia
  - skills
  - mcp
  - extensiones
  - automatización
category: "01 Stack"
subcategory: "IA"
author: Daniel
version: 2.0.0
status: active
related: []
---

# IA Skills

Sistema de skills y extensiones para modelos de IA. Permite crear comandos personalizados, integraciones y automatizaciones.

---

## 📚 Documentación Completa

### Guía Principal
[[Skills en Claude Code.md]] - Creación de skills en Claude Code
- Qué son los skills
- Estructura de un skill
- Cómo crear skills personalizados
- Integración con Claude
- Ejemplos prácticos

### Gestión de Skills
[[Skills.sh.md]] - Herramienta CLI para gestionar skills
- Instalación
- Comandos disponibles
- Crear nuevos skills
- Publicar skills
- Versioning

---

## 🔌 Model Context Protocol (MCP)

Estándar abierto para integrar herramientas con modelos de IA.

### Implementaciones Oficiales

#### Anthropic MCP
[[Anthropic-MCP.md]] - Implementación nativa de Anthropic
- Especificación MCP
- Servidor MCP
- Cliente MCP
- Protocol details
- Ejemplos

#### Vercel MCP
[[Vercel-MCP.md]] - Integraciones MCP de Vercel
- Vercel integrations
- Deploy automation
- Analytics
- Database connections
- Edge functions

---

## 🛠️ Tipos de Skills

### 1. **Comandos Personalizados**
Extend funcionalidad de herramientas con lógica personalizada

### 2. **Integraciones Externas**
Conectar APIs de terceros (GitHub, Jira, Slack, etc.)

### 3. **Automatizaciones**
Workflows automáticos basados en eventos o triggers

### 4. **Utilidades**
Herramientas para procesamiento, transformación de datos

---

## 📊 Arquitectura

```
Skill estructura:
├── manifest.json       # Metadata y configuración
├── src/
│   └── index.js        # Lógica principal
├── schemas/
│   └── input.json      # Input validation
└── README.md
```

---

## 🔗 Enlaces Relacionados

- [[IA-Agents/_README.md|IA Agents]] - Arquitectura de agentes
- [[IA-Tools/_README.md|IA Tools]] - Herramientas de IA
- [[../../README|Mapa de Stack]]

---

## 📚 Ecosistema de Skills

Skills disponibles en la comunidad:
- Desarrollo (Git, Code analysis, Refactoring)
- Integración (Slack, GitHub, Jira, Discord)
- Productividad (Email, Calendar, Docs)
- Datos (SQL, Analytics, Transformación)

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
