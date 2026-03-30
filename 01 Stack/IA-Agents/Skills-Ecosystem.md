---
title: Skills Ecosystem
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["MCP", "02-Herramientas-Desarrollo", "Agentes-IA"]
migrated: true
migratedFrom: 01 Stack/IA-Agents/Skills-Ecosystem.md
---

# Skills Ecosystem (skills.sh)

**Skills** son capacidades procedurales reutilizables que puedes instalar en agentes de IA con un solo comando.

## 🎯 ¿Qué es una Skill?

Una **skill** es un módulo de funcionalidad que:
- Agrega capacidades específicas a un agente
- Se instala con un comando simple
- Es reutilizable en diferentes agentes
- Contiene lógica, guías y ejemplos
- Se comparte en un ecosistema público

**Analogía:** Si los agentes son como smartphones, las skills son las aplicaciones que instalas desde una app store.

## 📦 Skills.sh - El ecosistema abierto

**skills.sh** es un directorio abierto con **20,000+ skills** mantenidas por la comunidad.

### Características

- ✅ **20,000+ skills** disponibles
- ✅ **775K+ installs mensuales** (skill más popular)
- ✅ Compatible con **20+ agentes** (Claude Code, Cursor, OpenCode, GitHub Copilot, VS Code, Windsurf, etc.)
- ✅ Instalación trivial: `npx skills add owner/repo/skill`
- ✅ Open source y comunitario
- ✅ Categorizado y buscable

### URL principal
https://skills.sh/

## 🚀 Cómo usar una Skill

### 1. Buscar una skill

```bash
# En https://skills.sh o por CLI
npx skills search react
npx skills search testing
```

### 2. Instalar una skill

```bash
# Formato: npx skills add owner/repo/skill-path
npx skills add vercel/agent-skills/vercel-react-best-practices
npx skills add anthropic/anthropic-sdk-python/usage

# Algunos ejemplos reales:
npx skills add anthropic/anthropic-sdk-python
npx skills add openai/community-python-sdk
npx skills add huggingface/transformers
```

### 3. La skill se integra en tu agente

El agente ahora tiene acceso a:
- Instrucciones (guías)
- Ejemplos de código
- Mejores prácticas
- APIs y herramientas específicas

### 4. Usar en tu workflow

```bash
# Prompt al agente incluyendo la skill
claude-code "Create a React component following Vercel best practices"
# El agente automáticamente usa la skill instalada
```

## 📚 Categorías populares

### Desarrollo Frontend
- `react` - React best practices
- `nextjs` - Next.js patterns
- `vue` - Vue.js guides
- `tailwind` - Tailwind CSS
- `astro` - Astro framework

### Desarrollo Backend
- `nodejs` - Node.js guides
- `python` - Python development
- `database` - Database patterns
- `api` - API design

### Testing & QA
- `jest` - Jest testing
- `cypress` - Cypress testing
- `vitest` - Vitest testing

### DevOps & Deployment
- `docker` - Docker containerization
- `kubernetes` - Kubernetes orchestration
- `vercel` - Vercel deployment
- `github-actions` - CI/CD with GitHub Actions

### IA & ML
- `openai` - OpenAI API
- `anthropic` - Anthropic models
- `llm` - LLM patterns

### Herramientas especializadas
- `obsidian-cli` - Obsidian vault automation
- `json-canvas` - JSON Canvas files
- `obsidian-markdown` - Markdown Obsidian syntax

## 📊 Skills vs MCP vs Plugins

| Característica | Skills | MCP | Plugins |
|---|---|---|---|
| **Instalación** | `npx skills add ...` | Configuración JSON | En editor |
| **Tipo de contenido** | Guías + ejemplos + lógica | Conexión a servicios | Extensiones |
| **Propósito** | Patrones y mejores prácticas | Acceso a herramientas externas | Funcionalidad adicional |
| **Actualización** | Automática | Manual | Manual |
| **Alcance** | Múltiples agentes | Múltiples agentes | Single agent/editor |
| **Comunidad** | Muy activa (20K+) | Pequeña pero creciendo | Varía por plataforma |

## 🏗️ Estructura de una Skill

```
skill-name/
├── README.md          # Documentación principal
├── guide.md           # Guía de uso
├── examples/          # Ejemplos de código
│   ├── basic.js
│   ├── advanced.js
│   └── typescript.ts
├── snippets/          # Fragmentos reutilizables
│   ├── component.jsx
│   └── hook.ts
├── references/        # Enlaces a documentación
│   └── links.md
└── manifest.json      # Metadatos de la skill
```

## 💻 Crear tu propia Skill

### 1. Estructura básica

```bash
mkdir my-custom-skill
cd my-custom-skill
```

### 2. Crear manifest.json

```json
{
  "name": "my-custom-skill",
  "version": "1.0.0",
  "description": "My custom skill for React patterns",
  "author": "Your Name",
  "license": "MIT",
  "categories": ["react", "frontend"],
  "agentCompatibility": ["claude-code", "opencode", "cursor"],
  "dependencies": {
    "react": "^18.0.0"
  }
}
```

### 3. Crear README.md

```markdown
# My Custom Skill

Descripción y guía de uso

## Instalación

\`\`\`bash
npx skills add your-username/repo/my-custom-skill
\`\`\`

## Uso

...
```

### 4. Agregar ejemplos

```javascript
// examples/basic.js
export const basicExample = () => {
  // Tu ejemplo aquí
};
```

### 5. Publicar en GitHub

```bash
git init
git add .
git commit -m "Initial commit"
git push origin main
```

### 6. Registrar en skills.sh

Ir a https://skills.sh/ y registrar tu skill.

## 🔗 Mis Skills instaladas

Puedo instalar skills relevantes para mi flujo:

```bash
# Obsidian automation
npx skills add anomaly/opencode/obsidian-cli

# MarkdownObsidian
npx skills add anomaly/opencode/obsidian-markdown

# JSON Canvas
npx skills add anomaly/opencode/json-canvas

# Vercel deployment
npx skills add vercel/agent-skills/vercel-deployment

# React best practices
npx skills add vercel/agent-skills/react-best-practices
```

## 🌟 Skills destacadas (775K+ installs)

Existe una skill tan popular que tiene más de 775K instalaciones mensuales. Aunque no está especificada, probablemente sea una relacionada con:
- React patterns
- Node.js best practices
- Testing utilities
- Deployment guides

## 📚 Recursos

- [skills.sh Official](https://skills.sh/)
- [Create a Skill Guide](https://skills.sh/docs/create)
- [Skills Marketplace](https://skills.sh/marketplace)
- [Community Discord](https://skills.sh/discord)

## 🔗 Referencias relacionadas

- [[Agentes-IA]] - Cómo usan los agentes las skills
- [[MCP]] - Alternativa para acceso a servicios externos
- [[02-Herramientas-Desarrollo]] - Agentes que soportan skills

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documentación inicial: qué son, cómo usar, crear, ecosistema |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
