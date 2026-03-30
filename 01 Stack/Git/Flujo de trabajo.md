---
title: "Git — Flujo de trabajo profesional"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - git
  - workflow
  - conventional-commits
  - versionado
category: "01 Stack"
subcategory: "Git"
author: Daniel
version: 1.0.0
status: active
related: []
---

# Git — Flujo de trabajo profesional

Git es el sistema de control de versiones estándar. Un buen flujo de trabajo es la diferencia entre un historial legible y una maraña de commits sin sentido.

---

## Feature branches — el flujo básico

```bash
# Siempre partir de main actualizado
git checkout main
git pull origin main

# Crear rama descriptiva
git checkout -b feat/user-authentication

# ... hacer cambios, commits parciales ...

# Mantener la rama al día con main 
git fetch origin

# Cuando está listo: abrir PR desde la UI de GitHub/GitLab
# Después del merge:
git checkout main
git pull origin main
git branch -d feat/user-authentication
```

---

## Conventional Commits

Formato estándar para mensajes de commit que permite generar changelogs automáticos y hace el historial legible:

```
<tipo>[scope opcional]: <descripción en imperativo>

[cuerpo opcional: qué y por qué]

[footer opcional: BREAKING CHANGE, refs]
```

**Tipos:**

| Tipo | Cuándo usarlo |
|------|--------------|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de bug |
| `docs` | Solo documentación |
| `style` | Formato, espacios (sin cambio lógico) |
| `refactor` | Refactoring sin nueva feature ni bug fix |
| `test` | Agregar o corregir tests |
| `chore` | Tareas de build, dependencias, CI |
| `perf` | Mejora de rendimiento |
| `ci` | Cambios en CI/CD |

```bash
# Ejemplos reales
git commit -m "feat(auth): add JWT refresh token rotation"
git commit -m "fix(api): handle null response from Strapi populate"
git commit -m "chore: upgrade TypeScript to 5.4"
git commit -m "refactor(users): extract getUserById into service layer"

# Con cuerpo cuando el contexto no es obvio
git commit -m "fix(uploads): limit file size to 10MB

Previous 50MB limit was causing OOM errors on the staging server.
The new limit is enforced at both client and server level.

Closes #142"

# Breaking change
git commit -m "feat(api)!: change pagination response format

BREAKING CHANGE: pagination metadata moved from root to meta.pagination"
```

---

## Rebase interactivo — reescribir historia local

Útil para limpiar commits antes de un PR (combinar WIPs, reordenar, mejorar mensajes):

```bash
# Reescribir los últimos 4 commits
git rebase -i HEAD~4

# En el editor aparece:
# pick abc1234 feat: add login form
# pick def5678 wip: fix validation
# pick ghi9012 wip: more fixes
# pick jkl3456 test: add login tests

# Cambiar a:
# pick abc1234 feat: add login form
# squash def5678 wip: fix validation     ← combinar con el anterior
# squash ghi9012 wip: more fixes         ← combinar con el anterior
# pick jkl3456 test: add login tests
```

> [!WARNING]
> El rebase reescribe la historia. Solo hazlo en ramas que NO has pusheado, o en ramas personales donde tú eres el único que trabaja. Nunca hagas `git rebase` en `main` o ramas compartidas.

---

## Cherry-pick — traer commits específicos

```bash
# Traer un commit específico de otra rama a la actual
git cherry-pick abc1234

# Traer múltiples commits
git cherry-pick abc1234 def5678

# Cherry-pick sin auto-commit (para revisar antes)
git cherry-pick --no-commit abc1234
```

Útil para hotfixes: puedes crear el fix en una rama de feature y llevarlo a main sin hacer merge de toda la rama.

---

## Stash — guardado temporal

```bash
# Guardar cambios actuales (tracked y untracked)
git stash push -u -m "WIP: formulario de contacto"

# Ver lista de stashes
git stash list
# stash@{0}: On feat/contact: WIP: formulario de contacto
# stash@{1}: On main: WIP: fix navbar

# Aplicar y eliminar el más reciente
git stash pop

# Aplicar sin eliminar
git stash apply stash@{1}

# Eliminar un stash específico
git stash drop stash@{1}
```

---

## git bisect — encontrar el commit que rompió algo

```bash
# Iniciar bisect
git bisect start

# Marcar el estado actual como malo
git bisect bad

# Marcar un commit donde funcionaba bien
git bisect good v2.1.0

# Git hace checkout a un commit intermedio — tú pruebas si funciona:
git bisect good  # si funciona
git bisect bad   # si está roto

# Git continúa bisectando hasta encontrar el commit exacto
# Al terminar:
git bisect reset
```

---

## Comandos del día a día

| Comando | Descripción |
|---------|-------------|
| `git log --oneline --graph` | Historial visual compacto |
| `git diff --staged` | Ver qué está en staging |
| `git add -p` | Stage interactivo (chunk por chunk) |
| `git commit --amend --no-edit` | Agregar al último commit sin cambiar mensaje |
| `git restore <file>` | Descartar cambios en un archivo |
| `git restore --staged <file>` | Unstage un archivo |
| `git branch -vv` | Ver ramas y su tracking remoto |
| `git fetch --prune` | Sincronizar y limpiar ramas remotas eliminadas |
| `git reflog` | Historial completo de operaciones (salvavidas) |

---

## Cómo revertir errores comunes

```bash
# "Committé en la rama equivocada"
git log --oneline -1         # anotar el hash del commit
git reset HEAD~1             # deshacer el commit (mantiene cambios)
git checkout rama-correcta
git cherry-pick <hash>

# "Hice push con credenciales o secrets"
# URGENTE: revocar el secret primero, luego:
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch config/secrets.yml" \
  --prune-empty --tag-name-filter cat -- --all
git push origin --force --all  # requiere autorización

# "Rompí algo después de un merge"
git revert <hash-del-merge> -m 1  # revierte el merge sin borrar historia

# "Perdí commits con reset --hard"
git reflog  # encontrar el hash del estado que quieres recuperar
git checkout -b recovery-branch <hash>
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
