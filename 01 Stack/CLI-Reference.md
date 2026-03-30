---
title: CLI Commands — Windows & macOS
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - cli
  - terminal
  - windows
  - macos
  - commands
  - npm
  - git
  - node
category: "01 Stack"
subcategory: "CLI-Reference"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 💻 CLI Commands — Windows & macOS

Referencia completa de comandos para terminal en Windows (PowerShell) y macOS/Linux (Bash).

---

## 🛠️ fnm y Homebrew (macOS/Windows Setup)

### fnm - Node Manager

**macOS Installation:**
```bash
brew install fnm

# Add to ~/.zshrc
eval "$(fnm env)"
source ~/.zshrc

# Verify
fnm --version
```

**Windows Installation:**
```powershell
choco install fnm
# Or download from github.com/Schniz/fnm/releases
```

**Basic Commands:**
```bash
fnm list                    # Ver versiones instaladas
fnm list-remote             # Ver versiones disponibles
fnm install 20              # Instalar Node 20
fnm use 20                  # Usar Node 20
fnm default 20              # Establecer como default
fnm install lts-latest      # Instalar LTS más reciente
```

**Create .nvmrc in Project:**
```bash
echo "20" > .nvmrc
# fnm cambia automáticamente al entrar a carpeta
```

### Homebrew - Package Manager (macOS)

**Installation:**
```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

brew --version
```

**Basic Commands:**
```bash
brew search node            # Buscar paquete
brew install node           # Instalar
brew list                   # Listar instalados
brew upgrade               # Actualizar todos
brew uninstall node        # Desinstalar
brew cleanup               # Limpiar
brew services start mongodb-community  # Iniciar servicio
```

**Useful Packages:**
```bash
brew install git
brew install mongodb-community
brew install postgresql
brew install redis
brew install docker
brew install fnm
```

---

## 🔑 Diferencias Clave

| Operación | PowerShell | Bash/macOS |
|-----------|-----------|-----------|
| Listar archivos | `ls` o `dir` | `ls` |
| Cambiar directorio | `cd` | `cd` |
| Ver ruta actual | `pwd` | `pwd` |
| Crear carpeta | `mkdir` | `mkdir` |
| Copiar archivo | `cp` | `cp` |
| Eliminar archivo | `rm` | `rm` |
| Ver contenido | `cat` | `cat` |
| Variable de entorno | `$env:VAR` | `$VAR` |
| Comentario | `#` | `#` |

---

## 📁 Navegación y Archivos

### Listar Archivos

**PowerShell (Windows):**
```powershell
# Listar archivos
ls
dir

# Con detalles
ls -la

# Filtrar por tipo
ls *.js
```

**Bash (macOS/Linux):**
```bash
# Listar archivos
ls
ls -la

# Con colores
ls -lah

# Recursivo
ls -R
```

### Cambiar Directorio

**PowerShell:**
```powershell
# Carpeta específica
cd "C:\Users\Daniel\Projects"

# Carpeta padre
cd ..

# Raíz
cd \

# Expandir ruta
cd ~\Documents
```

**Bash:**
```bash
# Carpeta específica
cd /Users/daniel/projects

# Carpeta padre
cd ..

# Home
cd ~

# Raíz
cd /
```

### Crear y Eliminar

**PowerShell:**
```powershell
# Crear carpeta
mkdir my-app
New-Item -ItemType Directory -Name my-app

# Crear archivo
New-Item -ItemType File -Name index.js

# Eliminar archivo
rm index.js
Remove-Item index.js

# Eliminar carpeta (recursivo)
rm -Recurse my-app
```

**Bash:**
```bash
# Crear carpeta
mkdir my-app

# Crear archivo vacío
touch index.js

# Eliminar archivo
rm index.js

# Eliminar carpeta (recursivo)
rm -rf my-app

# Copiar archivo
cp source.js destination.js

# Mover/renombrar
mv old-name.js new-name.js
```

### Ver Contenido

**PowerShell:**
```powershell
# Ver archivo completo
cat package.json
Get-Content package.json

# Primeras 10 líneas
Get-Content package.json -Head 10

# Últimas 10 líneas
Get-Content package.json -Tail 10

# Con número de línea
Get-Content package.json | Select-Object -Index 0..20
```

**Bash:**
```bash
# Ver archivo completo
cat package.json

# Primeras 10 líneas
head -n 10 package.json

# Últimas 10 líneas
tail -n 10 package.json

# Búsqueda en archivo
grep "dependencies" package.json

# Búsqueda recursiva
grep -r "dependencies" .
```

---

## 🔧 Variables de Entorno

### Listar Variables

**PowerShell:**
```powershell
# Todas las variables
ls env:

# Variable específica
$env:PATH
$env:HOME
$env:NODE_VERSION
```

**Bash:**
```bash
# Todas las variables
env

# Variable específica
echo $PATH
echo $HOME
echo $NODE_VERSION
```

### Establecer Variables

**PowerShell (Sesión Actual):**
```powershell
# Temporalmente
$env:NODE_ENV = "development"
$env:DATABASE_URL = "postgresql://localhost/mydb"
$env:API_KEY = "secret123"

# Ver variable
echo $env:NODE_ENV
```

**PowerShell (Permanente):**
```powershell
# En .env.local (mejor práctica)
# NODE_ENV=production
# DATABASE_URL=postgresql://...

# O variables de sistema
setx NODE_ENV "production"
setx DATABASE_URL "postgresql://localhost/mydb"

# Cerrar y abrir nueva terminal para que se aplique
```

**Bash (macOS):**
```bash
# Temporalmente
export NODE_ENV="development"
export DATABASE_URL="postgresql://localhost/mydb"

# Ver variable
echo $NODE_ENV

# Permanente (agregar a ~/.zshrc o ~/.bash_profile)
echo 'export NODE_ENV="development"' >> ~/.zshrc
source ~/.zshrc
```

---

## 📦 npm Commands

### Inicializar y Instalar

**PowerShell & Bash (Igual):**
```bash
# Inicializar proyecto
npm init
npm init -y

# Instalar dependencias
npm install
npm i

# Instalar paquete específico
npm install express
npm i express

# Instalar versión específica
npm install react@18.2.0

# Instalar globalmente
npm install -g vercel
npm i -g netlify-cli

# Instalar como dev dependency
npm install --save-dev typescript
npm i -D typescript
```

### Ver y Actualizar

```bash
# Listar paquetes instalados
npm list
npm ls

# Ver versiones de paquetes
npm outdated

# Actualizar paquete específico
npm update express

# Actualizar todos
npm update

# Verificar integridad
npm audit

# Corregir vulnerabilidades
npm audit fix
npm audit fix --force
```

### Ejecutar Scripts

```bash
# Script definido en package.json
npm start
npm run dev
npm run build
npm run test
npm run lint

# Script personalizado
npm run custom-script

# Ver todos los scripts disponibles
npm run
```

### Limpiar Cache

```bash
# Limpiar cache npm
npm cache clean --force

# Ver cache
npm cache verify

# Eliminar node_modules
npm cache clean
rm -rf node_modules
npm install
```

---

## 🌳 Git Commands

### Configuración Inicial

**PowerShell & Bash:**
```bash
# Verificar instalación
git --version

# Configurar usuario
git config --global user.name "Daniel"
git config --global user.email "daniel@example.com"

# Ver configuración
git config --global --list
```

### Clonar y Crear Repos

```bash
# Clonar repositorio
git clone https://github.com/user/repo.git
git clone https://github.com/user/repo.git my-folder

# Inicializar nuevo repo
git init
git init my-project

# Verificar estado
git status
git status --short
```

### Commits

```bash
# Ver cambios
git diff
git diff --staged

# Agregar archivos
git add .
git add file.js
git add "*.js"

# Commit
git commit -m "feat: add new feature"
git commit -am "fix: bug fix" # Stage y commit

# Ver commits
git log
git log --oneline
git log --graph --oneline --all

# Enmendar último commit
git commit --amend --no-edit
```

### Branches

```bash
# Listar branches
git branch
git branch -a

# Crear branch
git branch feature/new-feature
git checkout -b feature/new-feature

# Cambiar branch
git checkout main
git switch develop

# Eliminar branch
git branch -d feature/old-feature
git branch -D feature/force-delete

# Renombrar branch
git branch -m old-name new-name
```

### Push y Pull

```bash
# Push a remoto
git push
git push origin main
git push origin feature/my-feature

# Push y establecer upstream
git push -u origin feature/my-feature

# Pull cambios
git pull
git pull origin main

# Fetch sin mergear
git fetch
git fetch origin

# Mergear después de fetch
git merge origin/main
```

### Rebase y Merge

```bash
# Mergear branch
git merge feature/my-feature

# Rebase (limpiar historial)
git rebase main
git rebase -i HEAD~3 # Interactive rebase

# Abort rebase
git rebase --abort

# Cherry pick (aplicar commit específico)
git cherry-pick abc123
```

### Arreglar Errores

```bash
# Ver cambios sin stagear
git diff

# Unstage archivo
git restore --staged file.js
git reset HEAD file.js

# Descartar cambios locales
git checkout file.js
git restore file.js

# Undo último commit (sin perder cambios)
git reset --soft HEAD~1

# Undo último commit (perder cambios)
git reset --hard HEAD~1

# Ver historial de cambios
git reflog
git reset --hard abc123
```

### Stash

```bash
# Guardar cambios temporalmente
git stash
git stash save "WIP: feature en progreso"

# Ver stashes
git stash list

# Recuperar stash
git stash pop
git stash apply stash@{0}

# Eliminar stash
git stash drop stash@{0}
```

### Tags

```bash
# Crear tag
git tag v1.0.0
git tag -a v1.0.0 -m "Release 1.0.0"

# Listar tags
git tag

# Push tags
git push origin v1.0.0
git push origin --tags

# Eliminar tag
git tag -d v1.0.0
git push origin :v1.0.0
```

---

## 🚀 Node.js y npm Scripts Personalizados

### Ejecutar Scripts Directamente

```bash
# Node REPL interactivo
node
> 1 + 1
> 2
> .exit

# Ejecutar archivo JS
node index.js

# Pasar argumentos
node script.js arg1 arg2

# Con variables de entorno
NODE_ENV=production node app.js

# PowerShell
$env:NODE_ENV = "production"; node app.js

# Debugging
node --inspect app.js
node --inspect-brk app.js
```

### Scripts Útiles en package.json

```json
{
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "build": "tsc",
    "test": "jest",
    "lint": "eslint .",
    "format": "prettier --write .",
    "clean": "rm -rf dist node_modules",
    "setup": "npm install && npm run build",
    "postinstall": "npm run build"
  }
}
```

**Ejecutar:**
```bash
npm start
npm run dev
npm run build
npm test
npm run lint
npm run format
npm run clean
```

---

## 🔄 Operaciones Comunes

### Iniciar Proyecto Next.js

**PowerShell:**
```powershell
# Crear proyecto
npx create-next-app@latest my-app

# O con opciones específicas
npx create-next-app@latest my-app `
  --typescript `
  --tailwind `
  --eslint

cd my-app
npm run dev
```

**Bash:**
```bash
# Crear proyecto
npx create-next-app@latest my-app

# O con opciones
npx create-next-app@latest my-app \
  --typescript \
  --tailwind \
  --eslint

cd my-app
npm run dev
```

### Build y Deploy

**PowerShell:**
```powershell
# Build
npm run build

# Verificar en local
npm start

# Deploy a Vercel
npx vercel deploy --prod
```

**Bash:**
```bash
# Build
npm run build

# Verificar
npm start

# Deploy Netlify
netlify deploy --prod --dir=.next
```

### Limpiar Todo

**PowerShell:**
```powershell
# Eliminar node_modules y reinstalar
rm -Recurse node_modules
rm package-lock.json
npm install

# O en una sola línea
rm -Recurse node_modules, package-lock.json; npm install
```

**Bash:**
```bash
# Eliminar y reinstalar
rm -rf node_modules package-lock.json
npm install

# O una sola línea
rm -rf node_modules package-lock.json && npm install
```

---

## 📊 Alias Útiles (Bash)

Agregar a `~/.zshrc` o `~/.bash_profile`:

```bash
# Directorios
alias p="cd ~/projects"
alias d="cd ~/Documents"

# Git
alias gs="git status"
alias ga="git add"
alias gc="git commit"
alias gp="git push"
alias gl="git pull"
alias gb="git branch"

# npm
alias ni="npm install"
alias nid="npm install --save-dev"
alias nr="npm run"
alias nrb="npm run build"
alias nrd="npm run dev"
alias nrt="npm run test"

# Útiles
alias ll="ls -la"
alias cls="clear"
alias ..="cd .."
```

Luego:
```bash
source ~/.zshrc
gs    # git status
nrd   # npm run dev
ll    # ls -la
```

---

## 🪟 PowerShell Alias (Windows)

Agregar a tu perfil PowerShell:

```powershell
# Ver ubicación
$PROFILE

# Editar con notepad
notepad $PROFILE

# Agregar aliases
Set-Alias gs 'git status'
Set-Alias ga 'git add'
Set-Alias gc 'git commit'
Set-Alias gp 'git push'

function nrd { npm run dev }
function nrb { npm run build }
function ni { npm install }

# Usar en terminal
gs    # git status
nrd   # npm run dev
```

---

## 🚀 Quick Reference Card

```bash
# Node.js y npm
node --version
npm --version
npm init -y
npm install
npm run dev

# Git
git clone <url>
git add .
git commit -m "message"
git push origin main
git pull origin main

# Next.js
npx create-next-app@latest my-app
cd my-app
npm run dev
npm run build
npm start

# Netlify
netlify init
netlify deploy
netlify deploy --prod

# Vercel
vercel
vercel --prod
vercel env list
vercel env add KEY VALUE

# MongoDB
mongosh
use mydb
db.users.find()
db.users.insertOne({name: "Alice"})

# Prisma
npx prisma init
npx prisma db push
npx prisma generate
```

---

## 🔗 Enlaces Relacionados

- [[../Git/_README.md|Git Documentation]]
- [[../Node/Node.js.md|Node.js]]
- [[../Tech-Services/Netlify.md|Netlify CLI]]
- [[../Tech-Services/Vercel.md|Vercel CLI]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con comandos Windows/macOS |
