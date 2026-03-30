---
title: Agentes de IA
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["MCP", "02-Herramientas-Desarrollo", "Skills-Ecosystem"]
migrated: true
migratedFrom: 01 Stack/IA-Agents/Agentes-IA.md
---

# Agentes de IA

Un **agente de IA** es un sistema autónomo que puede:
- Recibir instrucciones en lenguaje natural
- Analizar el contexto y los objetivos
- Usar herramientas disponibles para ejecutar tareas
- Tomar decisiones y ajustarse según los resultados
- Iterar hasta lograr el objetivo

## 🎯 Características principales

### Autonomía
El agente trabaja de forma independiente sin intervención constante del usuario.

### Adaptabilidad
Puede cambiar su estrategia basándose en resultados intermedios.

### Uso de herramientas
Accede a APIs, servicios externos y contexto del proyecto mediante MCP servers.

### Razonamiento
Analiza problemas, planifica soluciones y ejecuta pasos en secuencia.

## 🛠️ Agentes populares para desarrollo

| Agente | Descripción | Proveedor | Uso |
|--------|-------------|-----------|-----|
| **Claude Code** | Agente CLI de Anthropic | Anthropic | Desarrollo con Claude 3.5+ |
| **OpenCode** | Agente CLI colaborativo | anomaly.co | Multi-herramienta, flexible |
| **GitHub Copilot** | Integración en editor | Microsoft/OpenAI | Autocomplete de código |
| **Cursor** | IDE basado en Claude Code | Anysphere | Editor + agente integrado |
| **Windsurf** | IDE con agente IA | Codeium | Editor + agente integrado |

## 🔄 Flujo de trabajo de un agente

```
1. Usuario proporciona instrucción/prompt
2. Agente analiza el objetivo y el contexto
3. Descompone en subtareas
4. Usa MCP servers para acceder a herramientas
5. Ejecuta código, crea archivos, consulta APIs
6. Valida resultados
7. Itera si es necesario
8. Reporta al usuario
```

## 📊 Diferencias clave

### Agentes vs ChatBots

| Aspecto | Agentes | ChatBots |
|--------|---------|---------|
| **Autonomía** | Alta (ejecuta acciones) | Media (genera texto) |
| **Herramientas** | Acceso a múltiples tools | Generalmente limitado |
| **Iteración** | Repite hasta lograr objetivo | Una respuesta por mensaje |
| **Caso de uso** | Tareas complejas, multistep | Conversación, consultas |

### Agentes vs Plugins

| Aspecto | Agentes | Plugins |
|--------|---------|---------|
| **Inteligencia** | Razonamiento automático | Funcionalidad adicional |
| **Control** | User → Agente decide | User controla completamente |
| **Complejidad** | Resuelve problemas complejos | Extensión específica |

## 🧠 Capacidades actuales (2026)

### ✅ Fortalezas
- Análisis de código y refactoring
- Escritura de nuevas funcionalidades
- Debugging y troubleshooting
- Creación de pruebas unitarias
- Documentación de código
- Integración con herramientas externas (MCP)
- Razonamiento con contexto del proyecto

### ⚠️ Limitaciones
- Contexto limitado (ventana de tokens)
- No pueden ejecutar directamente en máquina del usuario sin CLI
- Dependen de instrucciones claras
- Pueden hacer errores en tareas muy complejas
- No tienen memoria persistente entre sesiones (sin persistencia explícita)

## 🔗 Conceptos relacionados

- [[MCP]] - Protocolo para conectar herramientas a agentes
- [[Skills-Ecosystem]] - Habilidades reutilizables para agentes
- [[02-Herramientas-Desarrollo]] - Agentes específicos disponibles

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documentación inicial: conceptos, características, flujos |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
