---
title: Agent Skills Directory - Skills.sh
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [skills, agentes, ia, herramientas, ecosystem]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["01 Tecnolog\u00edas/Herramientas/IA/README", "Configuraci\u00f3n Ejemplo", "Skills/Anthropic", "Skills/Vercel", "Claude Code", "OpenCode"]
migrated: true
migratedFrom: 01 Stack/IA-Skills/Skills.sh.md
---

# 🛠️ Agent Skills Directory (skills.sh)

Ecosistema abierto de habilidades reutilizables para agentes de IA. Instala skills con un solo comando para potenciar tus agentes con acceso a conocimiento procedural.

- **Web**: [skills.sh](https://skills.sh)
- **Directorio**: [skills.sh - Leaderboard](https://skills.sh)
- **Docs**: [skills.sh/docs](https://skills.sh/docs)
- **GitHub**: [github.com/vercel-labs/skills](https://github.com/vercel-labs/skills)

---

## 📖 ¿Qué son Agent Skills?

**Skills** son capacidades reutilizables para agentes de IA que proporcionan:

- **Conocimiento procedural** - Cómo hacer cosas
- **Guías de mejores prácticas** - Patrones probados
- **Integraciones** - Conexiones a servicios externos
- **Librerías y herramientas** - Acceso a APIs y funcionalidades

### Ejemplo

En lugar de que el agente aprenda desde cero sobre React, instala el skill `vercel-react-best-practices`:

```bash
npx skills add vercel-labs/agent-skills/vercel-react-best-practices
```

El agente ahora tiene acceso a:
- Patrones de React modernos
- Mejores prácticas
- Errores comunes
- Soluciones recomendadas

---

## 🎯 Características Principales

| Característica | Descripción |
|---|---|
| **Instalación simple** | `npx skills add owner/repo/skill` |
| **Reutilizable** | Una vez instalado, disponible para todos los agentes |
| **Abierto** | Cualquiera puede crear y compartir skills |
| **Versionado** | Control de versiones de skills |
| **Descubrible** | Leaderboard de skills populares |
| **Multi-agente** | Compatible con 20+ agentes diferentes |

---

## 🤖 Agentes Soportados

Skills.sh funciona con los siguientes agentes:

| Agente | Descripción |
|--------|-------------|
| **Claude Code** | IDE de Anthropic para código |
| **Cursor** | Editor basado en VSCode |
| **Cline** | Agente de Anthropic |
| **GitHub Copilot** | Asistente de GitHub |
| **VS Code** | Editor de Microsoft |
| **Windsurf** | Editor inteligente de Codeium |
| **OpenCode** | CLI colaborativo [[OpenCode]] |
| **Roo Code** | Agente de desarrollo |
| **Google Gemini** | Agente de Google |
| **Google Antigravity** | Plataforma de Google |
| **Goose** | Agente de BlockCraft |
| **AMP Code** | IDE AMP |
| **Codex** | Modelo de OpenAI |
| **Nous Research** | Agente de Nous |
| **Kilo** | Plataforma Kilo |
| **Kiro CLI** | CLI de Kiro |
| **Trae** | Agente de Trae |
| **ClawdBot** | Bot de Claude |

---

## 📦 Instalación

### Instalación Básica

```bash
# Instalar un skill individual
npx skills add owner/repo/skill-name

# Ejemplos:
npx skills add vercel-labs/agent-skills/vercel-react-best-practices
npx skills add anthropics/skills/frontend-design
npx skills add microsoft/github-copilot-for-azure/azure-ai
```

### Instalación Global

```bash
# Instalar CLI de skills globalmente
npm install -g @skills/cli

# Luego usar:
skills add vercel-labs/agent-skills/web-design-guidelines
```

### En Archivo de Configuración

Muchos agentes permiten listar skills en un archivo de configuración:

```json
{
  "skills": [
    "vercel-labs/agent-skills/vercel-react-best-practices",
    "anthropics/skills/frontend-design",
    "microsoft/github-copilot-for-azure/azure-ai"
  ]
}
```

---

## 🏆 Skills Populares

### Top 5 Skills (por instalaciones)

| # | Nombre | Autor | Instalaciones |
|---|--------|-------|----------------|
| 1 | **find-skills** | vercel-labs | 775.3K |
| 2 | **vercel-react-best-practices** | vercel-labs | 261.2K |
| 3 | **frontend-design** | anthropics | 218.6K |
| 4 | **web-design-guidelines** | vercel-labs | 210.7K |
| 5 | **remotion-best-practices** | remotion-dev | 187.4K |

---

## 🎓 Categorías de Skills

### Frontend Development

| Skill | Descripción |
|-------|-------------|
| **vercel-react-best-practices** | Patrones y mejores prácticas de React |
| **frontend-design** | Principios de diseño frontend |
| **web-design-guidelines** | Guías de diseño web moderno |
| **shadcn-ui** | Componentes de shadcn/ui |
| **tailwind-design-system** | Sistemas de diseño con Tailwind |

### Backend Development

| Skill | Descripción |
|-------|-------------|
| **nodejs-backend-patterns** | Patrones backend con Node.js |
| **api-design-principles** | Principios de diseño de APIs |
| **better-auth-best-practices** | Autenticación moderna |
| **supabase-postgres-best-practices** | Mejores prácticas con Postgres |

### Cloud & DevOps

| Skill | Descripción |
|-------|-------------|
| **azure-ai** | Servicios de IA en Azure |
| **microsoft-foundry** | Foundry de Microsoft |
| **deploy-to-vercel** | Deploy automático en Vercel |
| **github-actions-docs** | Documentación de GitHub Actions |

### Testing & QA

| Skill | Descripción |
|-------|-------------|
| **webapp-testing** | Testing de aplicaciones web |
| **playwright-best-practices** | Mejores prácticas con Playwright |
| **test-driven-development** | Desarrollo orientado a tests |

### AI & Generación

| Skill | Descripción |
|-------|-------------|
| **ai-image-generation** | Generación de imágenes con IA |
| **ai-video-generation** | Generación de vídeos con IA |
| **elevenlabs-tts** | Text-to-Speech con ElevenLabs |
| **skill-creator** | Crear tus propios skills |

### Marketing & SEO

| Skill | Descripción |
|-------|-------------|
| **seo-audit** | Auditoría SEO completa |
| **content-strategy** | Estrategia de contenidos |
| **copywriting** | Técnicas de copywriting |
| **marketing-psychology** | Psicología en marketing |

---

## 🔨 Crear tu Propio Skill

### Estructura Básica

```bash
# Crear repositorio
mkdir my-skill
cd my-skill

# Crear estructura
mkdir -p skills/my-skill
touch skills/my-skill/skill.md
```

### skill.md - Plantilla

```markdown
---
title: My Custom Skill
description: What this skill teaches agents
version: 1.0.0
---

# My Awesome Skill

## Overview
Descripción de qué hace el skill...

## Key Concepts
- Concepto 1
- Concepto 2
- Concepto 3

## Best Practices
1. Práctica 1
2. Práctica 2

## Examples
\`\`\`javascript
// Ejemplo de código
\`\`\`

## Common Pitfalls
- Error común 1
- Error común 2

## Learn More
- [Resource 1](https://...)
- [Resource 2](https://...)
```

### Publicar tu Skill

```bash
# 1. Hacer push a GitHub
git add .
git commit -m "Add my-skill"
git push origin main

# 2. Registrar en skills.sh
# Ir a https://skills.sh y seguir instrucciones

# 3. Usuarios pueden instalar
npx skills add username/repo/my-skill
```

---

## 📚 Ejemplos de Uso

### Ejemplo 1: Aprender React Best Practices

```bash
# Instalar skill de React
npx skills add vercel-labs/agent-skills/vercel-react-best-practices

# Ahora el agente sabe:
# - Cómo usar hooks correctamente
# - Optimización de performance
# - Patrones modernos
# - Errores comunes y cómo evitarlos
```

### Ejemplo 2: Frontend Design

```bash
# Instalar skill de diseño
npx skills add anthropics/skills/frontend-design

# El agente puede ahora:
# - Seguir principios de diseño
# - Crear interfaces modernas
# - Aplicar mejores prácticas UX
# - Usar librerías de componentes
```

### Ejemplo 3: Agente Multi-skill

```bash
# Instalar varios skills
npx skills add vercel-labs/agent-skills/vercel-react-best-practices
npx skills add anthropics/skills/frontend-design
npx skills add vercel-labs/agent-skills/deploy-to-vercel
npx skills add anthropics/skills/webapp-testing

# Tu agente ahora es experto en:
# 1. Escribir React con mejores prácticas
# 2. Diseño frontend moderno
# 3. Deploy en Vercel
# 4. Testing de apps web
```

---

## 🎯 Casos de Uso

### 1. Desarrollo Web Completo

```bash
# Frontend
npx skills add vercel-labs/agent-skills/vercel-react-best-practices
npx skills add anthropics/skills/frontend-design

# Backend
npx skills add wshobson/agents/nodejs-backend-patterns
npx skills add anthropics/skills/web-artifacts-builder

# Deployment
npx skills add vercel-labs/agent-skills/deploy-to-vercel

# Testing
npx skills add anthropics/skills/webapp-testing
```

### 2. Agente Especializado en Marketing

```bash
npx skills add coreyhaines31/marketingskills/copywriting
npx skills add coreyhaines31/marketingskills/content-strategy
npx skills add coreyhaines31/marketingskills/seo-audit
npx skills add coreyhaines31/marketingskills/social-content
npx skills add coreyhaines31/marketingskills/pricing-strategy
```

### 3. Agente DevOps/Cloud

```bash
npx skills add microsoft/github-copilot-for-azure/azure-ai
npx skills add microsoft/github-copilot-for-azure/azure-compute
npx skills add microsoft/github-copilot-for-azure/azure-kubernetes
npx skills add github/awesome-copilot/github-actions-docs
```

---

## 🔍 Descubrir Skills

### En la Web

Visita [skills.sh](https://skills.sh) para:
- Ver leaderboard de skills populares
- Buscar skills por categoría
- Ver trending skills (24h)
- Filtrar por agente soportado

### Por Línea de Comandos

```bash
# Ver skills disponibles
npx skills list

# Buscar skills
npx skills search react

# Ver detalles de un skill
npx skills info vercel-labs/agent-skills/vercel-react-best-practices

# Ver skills trending
npx skills trending
```

---

## ⚙️ Configuración

### En Claude Code

```bash
# Crear archivo .skills.json
{
  "skills": [
    "anthropics/skills/frontend-design",
    "anthropics/skills/webapp-testing",
    "vercel-labs/agent-skills/vercel-react-best-practices"
  ]
}
```

### En OpenCode

```bash
# Configuración en config.json
{
  "skills": [
    "vercel-labs/agent-skills/web-design-guidelines",
    "microsoft/github-copilot-for-azure/azure-ai",
    "anthropics/skills/skill-creator"
  ]
}
```

### En Cursor

```bash
# Settings en VSCode
{
  "cursor.skills": [
    "vercel-labs/agent-skills/vercel-react-best-practices",
    "anthropics/skills/frontend-design"
  ]
}
```

---

## 🤝 Contribuir Skills

### Pasos para Contribuir

1. **Crear repositorio**
   ```bash
   mkdir my-skills
   cd my-skills
   git init
   ```

2. **Crear estructura**
   ```bash
   mkdir -p skills/my-skill-1 skills/my-skill-2
   ```

3. **Escribir skills**
   - Crear `skill.md` en cada carpeta
   - Documentar con ejemplos
   - Incluir mejores prácticas

4. **Publicar**
   ```bash
   git add .
   git commit -m "Add my skills"
   git push origin main
   ```

5. **Registrar en skills.sh**
   - Ir a [skills.sh](https://skills.sh)
   - Hacer submit del repositorio
   - Esperar validación

### Requerimientos de Calidad

- ✅ Documentación clara
- ✅ Ejemplos prácticos
- ✅ Mejores prácticas
- ✅ Casos comunes y edge cases
- ✅ Enlaces a recursos
- ✅ Actualizado

---

## 📊 Estadísticas

| Métrica | Cantidad |
|---------|----------|
| **Total de skills** | 20,000+ |
| **Installs totales** | 90,498K+ |
| **Agentes soportados** | 20+ |
| **Organizaciones** | 100+ |
| **Skills trending** | Actualizados 24h |

---

## 🐛 Troubleshooting

### Error: "Skill not found"

```bash
# Verificar nombre exacto
npx skills search vercel-react

# Usar nombre completo correcto
npx skills add vercel-labs/agent-skills/vercel-react-best-practices
```

### Skill no aparece en agente

```bash
# Reinstalar skill
npx skills remove vercel-labs/agent-skills/vercel-react-best-practices
npx skills add vercel-labs/agent-skills/vercel-react-best-practices

# Reiniciar agente
# (Varía según agente usado)
```

### Conflicto de skills

```bash
# Si dos skills entran en conflicto:
npx skills list      # Ver skills instalados
npx skills remove X  # Remover el que causa conflicto
npx skills add Y     # Agregar alternativa
```

---

## 🔗 Referencias Relacionadas

- [[Claude Code]] - Usar skills con Claude Code
- [[OpenCode]] - Usar skills con OpenCode
- [[Configuración Ejemplo]] - Ejemplos de configuración de skills
- [[01 Tecnologías/Herramientas/IA/README]] - Índice de herramientas IA
- [[Skills/Anthropic]] - Skills de Anthropic
- [[Skills/Vercel]] - Skills de Vercel

---

## 📚 Recursos Externos

- **Sitio oficial**: [skills.sh](https://skills.sh)
- **Documentación**: [skills.sh/docs](https://skills.sh/docs)
- **GitHub**: [vercel-labs/skills](https://github.com/vercel-labs/skills)
- **Crear skills**: [skills.sh - Guía](https://skills.sh/docs/creating-skills)
- **Comunidad**: [Discussions en GitHub](https://github.com/vercel-labs/skills/discussions)

---

## 🎓 Aprende Más

### Skills Recomendados para Comenzar

1. **find-skills** - Descubre más skills
2. **frontend-design** - Principios de diseño
3. **vercel-react-best-practices** - React moderno
4. **webapp-testing** - Testing web

### Crear tu Primer Skill

```bash
# Tutoriales disponibles en:
# https://skills.sh/docs/creating-skills

# Template básico
npx skills create my-skill
```

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Documentación inicial completa de skills.sh |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
