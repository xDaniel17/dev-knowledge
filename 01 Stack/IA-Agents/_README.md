---
title: IA Agents - Arquitectura y Ecosistema
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - ia
  - agents
  - mcp
  - skills
  - prompting
category: "01 Stack"
subcategory: "IA"
author: Daniel
version: 2.0.0
status: active
related: []
---

# IA Agents

Documentación de arquitecturas de agentes de IA, Model Context Protocol (MCP), skills y técnicas avanzadas de prompting.

---

## 🤖 Fundamentos de Agentes

[[Agentes-IA.md]] - Introducción a los agentes de inteligencia artificial
- Qué son los agentes
- Cómo funcionan
- Flujo de pensamiento (Chain of Thought)
- Alucinaciones y mitigation
- Aplicaciones prácticas

---

## 🔌 Model Context Protocol (MCP)

[[MCP.md]] - Estándar para conectar modelos de IA con herramientas
- Qué es MCP
- Arquitectura cliente-servidor
- Implementación de servidores MCP
- Recursos, herramientas y prompts
- Casos de uso

### Implementaciones:
- [[IA-Skills/Anthropic-MCP.md|Anthropic MCP]]
- [[IA-Skills/Vercel-MCP.md|Vercel MCP]]

---

## 🛠️ Skills Ecosystem

[[Skills-Ecosystem.md]] - Sistema de comandos y extensiones para agentes
- Qué son los skills
- Estructura de un skill
- Cómo crear skills
- Integración con Claude Code
- Casos de uso

### Skills específicos:
- [[IA-Skills/_README.md|Skills en Detail]]
- [[IA-Skills/Skills.sh.md|Skills.sh - Gestión de Skills]]

---

## 💡 Prompting Avanzado

[[Prompting avanzado.md]] - Técnicas profesionales de prompting
- Chain of Thought (CoT)
- Few-shot learning
- XML structured prompts
- Context optimization
- Mejores prácticas

---

## 🔗 Herramientas Relacionadas

- [[IA-Tools/_README.md|IA Tools]] - OpenCode, Claude Code, GitHub Copilot
- [[IA-Skills/_README.md|IA Skills]] - Sistema de skills y extensiones

---

## 📚 Flujo Típico de un Agente

```
1. Input del usuario
   ↓
2. System prompt (contexto y instrucciones)
   ↓
3. Chain of Thought (razonamiento)
   ↓
4. Tool calls (invoca herramientas/skills)
   ↓
5. Procesamiento de respuestas
   ↓
6. Refinamiento o iteración
   ↓
7. Output al usuario
```

---

## 📊 Aplicaciones Prácticas

- **Agentes de Desarrollo**: OpenCode, Claude Code (análisis de código, refactoring)
- **Agentes de Investigación**: Búsqueda, síntesis, análisis
- **Agentes de Automatización**: Procesos, integraciones, workflows
- **Agentes Conversacionales**: Asistentes, chatbots, helpdesk

---

## 🔗 Enlaces Relacionados

- [[../../README|Mapa de Stack]]
- [[../../00 Fundamentos/README|Fundamentos]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
