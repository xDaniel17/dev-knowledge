---
title: Git - Control de Versiones
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - git
  - vcs
  - colaboración
  - devops
  - workflows
category: "01 Stack"
subcategory: "Git"
author: Daniel
version: 2.0.0
status: active
related: []
---

# Git

Sistema distribuido de control de versiones. Esencial para trabajo colaborativo y seguimiento de cambios.

---

## 📚 Documentación Completa

### Conceptos Fundamentales
[[Git.md]] - Introducción a Git
- Qué es Git
- Repositorios
- Commits
- Branches
- Remote repositories

### Referencia de Comandos
[[Comandos.md]] - Guía completa de comandos
- Clone, Pull, Push
- Commit, Add, Status
- Branch, Checkout, Merge
- Rebase, Squash
- Stash, Cherry-pick

### Ejemplos Prácticos
[[Ejemplos.md]] - Workflow real y casos de uso
- Comenzar un proyecto
- Feature branches
- Merge y resolución de conflictos
- Rebase interactivo
- Undo y recover

### Workflow
[[Flujo de trabajo.md]] - Estrategias de integración
- Git Flow
- GitHub Flow
- Trunk-Based Development
- Convenciones de commits
- Pull requests

---

## 🔑 Conceptos Clave

### Estructura Base

```
Workspace (archivos locales)
    ↓ (git add)
Index/Staging area (archivos preparados)
    ↓ (git commit)
Local Repository (.git/)
    ↓ (git push)
Remote Repository (GitHub/GitLab)
```

### Branching Strategies

#### Git Flow
```
main (production)
├── hotfix/
└── release/

develop (next release)
├── feature/issue-123
├── feature/refactor
└── bugfix/critical
```

#### GitHub Flow (simple)
```
main (always deployable)
├── feature/new-ui
├── feature/api-v2
└── bugfix/login
```

---

## 📊 Comandos Esenciales

```bash
# Setup
git init
git clone <url>
git config user.name "Daniel"

# Daily workflow
git add <file>
git commit -m "feat: add feature"
git push origin <branch>
git pull origin <branch>

# Branching
git branch <name>
git checkout -b <name>
git merge <branch>
git branch -d <name>

# Inspection
git log --oneline
git diff
git status
git show <commit>

# Fixes
git reset HEAD~1          # Undo last commit
git revert <commit>      # Create inverse commit
git cherry-pick <commit> # Apply specific commit
```

---

## 🔗 Enlaces Relacionados

- [[../../../README|Mapa de Stack]]
- [[../../02 Flujos/_README.md|Flujos de Trabajo]]

---

## 💡 Best Practices

1. **Commits pequeños y enfocados** - Un cambio por commit
2. **Mensajes descriptivos** - Explain "why", not just "what"
3. **Branch names claros** - feature/..., bugfix/..., hotfix/...
4. **Pull requests para review** - Colaboración y calidad
5. **Sincronizar regularmente** - Evitar conflictos

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
