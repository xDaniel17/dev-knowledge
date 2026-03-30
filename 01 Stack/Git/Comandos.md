---
title: Git & Github
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [tecnologias, desarrollo]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/Git/Comandos.md
---

# Git & Github

## Comandos Básicos

```bash
# Iniciar repositorio
git init

# Añadir archivos
git add .
git add -A

# Commit
git commit -m "mensaje"

# Ramas
git branch -M main
git checkout -b rama

# Remoto
git remote add origin url
git push -u origin main

# Pull y fetch
git pull origin main
git fetch origin
```

## Estados

```
Working Dir → Staging → Repository
```

## Configuración

```bash
git config --global user.name "Nombre"
git config --global user.email "email"
```

## Recursos

- [Documentación Git](https://git-scm.com/doc)
- [Documentación GitHub](https://docs.github.com)

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
