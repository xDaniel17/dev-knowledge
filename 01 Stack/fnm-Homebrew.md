---
title: fnm y Homebrew - Gestores de Versiones y Paquetes
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - fnm
  - homebrew
  - node-manager
  - package-manager
  - macos
  - windows
  - tools
category: "01 Stack"
subcategory: "CLI-Tools"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🛠️ fnm y Homebrew - Gestores de Versiones y Paquetes

fnm (Fast Node Manager) para gestionar versiones de Node.js. Homebrew para instalar herramientas en macOS. Esenciales para desarrolladores.

---

## 🎯 ¿Por Qué Necesitas Estos?

### fnm (Fast Node Manager)
- **Cambiar versiones de Node.js** sin problemas
- **Múltiples proyectos** con diferentes versiones
- **Instalación rápida** de nuevas versiones
- **Compatible** con .nvmrc

### Homebrew
- **Gestor de paquetes** para macOS (como apt en Linux)
- **Instalar herramientas** fácilmente (git, node, etc.)
- **Actualizaciones** automáticas
- **Desinstalar limpiamente**

---

## 📦 Homebrew (macOS)

### Instalación

**En terminal macOS:**
```bash
# Instalación oficial
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Verificar instalación
brew --version

# Ubicación típica
/usr/local/bin/brew  # Intel Macs
/opt/homebrew/bin/brew  # Apple Silicon (M1/M2/M3)
```

Si se instala en `/opt/homebrew`, agregar a PATH:

```bash
# En ~/.zshrc o ~/.bash_profile
export PATH="/opt/homebrew/bin:$PATH"

# Aplicar cambios
source ~/.zshrc
```

### Comandos Básicos

```bash
# Buscar paquete
brew search node
brew search mongodb

# Instalar paquete
brew install node
brew install git
brew install mongodb-community
brew install postgresql
brew install redis

# Instalar específica versión
brew install node@18
brew install mongodb-community@6.0

# Listar paquetes instalados
brew list

# Ver información del paquete
brew info node

# Desinstalar paquete
brew uninstall node

# Actualizar Homebrew
brew update

# Actualizar todos los paquetes
brew upgrade

# Actualizar paquete específico
brew upgrade node

# Limpiar archivos innecesarios
brew cleanup

# Verificar problemas
brew doctor
```

### Servicios (Bases de Datos, etc.)

```bash
# Iniciar servicio
brew services start mongodb-community
brew services start postgresql
brew services start redis

# Detener servicio
brew services stop mongodb-community

# Reiniciar servicio
brew services restart redis

# Listar servicios activos
brew services list

# Ver logs
brew services log mongodb-community
```

### Ejemplos Prácticos

**Instalar stack MERN en macOS:**

```bash
# Node.js
brew install node

# MongoDB
brew install mongodb-community
brew services start mongodb-community

# PostgreSQL (alternativa)
brew install postgresql
brew services start postgresql

# Redis para caching
brew install redis
brew services start redis

# Git
brew install git

# Verificar todo funciona
node --version
npm --version
mongosh
psql --version
redis-cli ping
```

**Actualizar todo:**

```bash
brew update && brew upgrade
```

---

## ⚡ fnm (Fast Node Manager)

### Instalación

**macOS (Homebrew):**
```bash
brew install fnm

# Verificar instalación
fnm --version
```

**macOS (Manual/Curl):**
```bash
curl -fsSL https://fnm.io/install | bash

# Agregar a PATH (~/.zshrc o ~/.bash_profile)
export PATH="/Users/$(whoami)/.fnm:$PATH"
eval "$(fnm env)"

# Aplicar
source ~/.zshrc
```

**Windows (Chocolatey):**
```powershell
choco install fnm

# O descargar desde github.com/Schniz/fnm/releases
# Agregar a PATH manualmente
```

**Windows (PowerShell):**
```powershell
# Con scoop (si tienes scoop instalado)
scoop install fnm

# O descargar ejecutable desde releases de GitHub
```

### Comandos Básicos

```bash
# Ver versión de fnm
fnm --version

# Listar versiones instaladas
fnm list

# Listar versiones disponibles
fnm list-remote

# Instalar versión específica
fnm install 18
fnm install 20.10.0
fnm install lts-latest

# Usar versión específica
fnm use 18
fnm use 20

# Establecer como default
fnm default 20

# Cambiar versión actual (temporal)
fnm use 18

# Ver versión actual
node --version
```

### .nvmrc (Especificar Versión por Proyecto)

**En root del proyecto:**
```bash
# Crear archivo .nvmrc
echo "18" > .nvmrc

# O
echo "20.10.0" > .nvmrc
```

**Al entrar a carpeta:**
```bash
# fnm usa automáticamente la versión en .nvmrc
cd mi-proyecto
node --version # Usa versión de .nvmrc

# O hacer manualmente
fnm use
```

### Alias útiles (Bash/macOS)

Agregar a `~/.zshrc`:

```bash
# fnm aliases
alias fnl="fnm list"
alias fnlr="fnm list-remote | head -20"
alias fni="fnm install"
alias fnu="fnm use"
alias fnd="fnm default"
```

Usar:
```bash
fnl        # fnm list
fni 20     # fnm install 20
fnu 18     # fnm use 18
```

---

## 🔄 Casos de Uso Prácticos

### Caso 1: Múltiples Proyectos con Diferentes Versiones

```bash
# Proyecto 1 (Node 18)
cd ~/projects/proyecto-legacy
echo "18" > .nvmrc
npm install

# Proyecto 2 (Node 20)
cd ~/projects/proyecto-nuevo
echo "20" > .nvmrc
npm install

# Al cambiar carpetas, fnm cambia automáticamente
cd ~/projects/proyecto-legacy
node --version  # v18.x.x

cd ~/projects/proyecto-nuevo
node --version  # v20.x.x
```

### Caso 2: Probar Versión Nueva

```bash
# Versión actual
fnm list

# Instalar nueva versión
fnm install 22

# Probar en proyecto
cd ~/test-project
fnm use 22
npm test

# Volver a versión anterior
fnm use 20
```

### Caso 3: Setup Completo de Desarrollo

**macOS:**

```bash
# 1. Instalar Homebrew
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# 2. Instalar fnm con Homebrew
brew install fnm

# 3. Agregar fnm a shell
echo 'eval "$(fnm env)"' >> ~/.zshrc
source ~/.zshrc

# 4. Instalar Node.js LTS
fnm install lts-latest
fnm default lts-latest

# 5. Instalar herramientas útiles
brew install git
brew install mongodb-community
brew install postgresql
brew install redis
brew install docker

# 6. Iniciar servicios
brew services start mongodb-community
brew services start postgresql
brew services start redis

# 7. Instalar CLI tools globalmente
npm install -g vercel
npm install -g netlify-cli
npm install -g next
npm install -g create-react-app
```

**Windows (PowerShell):**

```powershell
# 1. Instalar fnm (con Chocolatey)
choco install fnm

# 2. Instalar Node con fnm
fnm install lts-latest
fnm default lts-latest

# 3. Instalar Git
choco install git

# 4. Instalar herramientas
choco install mongodb
choco install postgresql
choco install redis

# 5. Instalar CLI tools
npm install -g vercel
npm install -g netlify-cli
npm install -g next
```

---

## 🔧 Configuración Avanzada

### fnm Config

```bash
# Ver ubicación de config
fnm env

# Variables de entorno
export FNM_DIR="$HOME/.fnm"
export FNM_USING_COREPACK=true
export FNM_NODE_DIST_MIRROR=https://nodejs.org/dist/
```

### Usar fnm con CI/CD

**GitHub Actions:**
```yaml
name: Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node
        uses: Schniz/fnm-action@master
        with:
          fnm-version: latest
          node-version: 18
      
      - name: Install
        run: npm install
      
      - name: Test
        run: npm test
```

---

## 🐛 Troubleshooting

### fnm: command not found (macOS)

```bash
# Agregar a ~/.zshrc
eval "$(fnm env)"

# Aplicar
source ~/.zshrc

# Verificar
fnm --version
```

### Versión no cambia después de fnm use

```bash
# Rehash (shell hash table)
hash -r

# O reiniciar terminal
```

### MongoDB no inicia después de Homebrew install

```bash
# Crear directorio de datos
mkdir -p /usr/local/var/mongodb

# Iniciar servicio
brew services start mongodb-community

# Ver estado
brew services list
```

### PostgreSQL password forgotten

```bash
# Resetear usuario postgres
brew services stop postgresql
brew uninstall postgresql
brew install postgresql
brew services start postgresql

# Crear nuevo usuario
createuser -P admin
```

---

## 📊 Comparison: fnm vs nvm vs n

| Feature | fnm | nvm | n |
|---------|-----|-----|---|
| **Speed** | ⚡ Muy rápido | Rápido | Rápido |
| **Installation** | Fácil (Homebrew) | Más complejo | Sencillo |
| **Soporte Windows** | ✅ Sí | ❌ No (WSL) | ❌ No |
| **Auto-switch (.nvmrc)** | ✅ Sí | ✅ Sí | ✅ Sí |
| **Mantenimiento** | ✅ Activo | ✅ Activo | ⚠️ Menos activo |
| **Recomendado** | ✅ Mejor opción | ✅ Buena opción | ⚠️ Alternativa |

---

## 💡 Best Practices

1. **Usa .nvmrc en proyectos** - Evita conflictos de versiones
2. **Mantén fnm/Homebrew actualizado** - `brew update && brew upgrade`
3. **Pin versions en .nvmrc** - Especifica versión exacta si necesario
4. **Usa fnm default** - Establece versión global consistente
5. **Documenta requisitos** - En README.md
6. **Síncroniza con package.json** - Mantén consistencia

---

## 📝 Ejemplo README con Requisitos

```markdown
# Mi Aplicación

## Requisitos

- Node.js 20+ (usar fnm para instalar)
- npm 10+
- MongoDB 6.0+ (instalar con Homebrew en macOS)

## Setup

### macOS

\`\`\`bash
# Instalar fnm
brew install fnm

# Instalar Node.js
fnm install
fnm default lts-latest

# Instalar MongoDB
brew install mongodb-community
brew services start mongodb-community

# Setup de proyecto
npm install
npm run dev
\`\`\`

### Windows

\`\`\`powershell
# Instalar fnm con Chocolatey
choco install fnm

# Instalar Node.js
fnm install lts-latest

# MongoDB en Docker (recomendado)
docker run -d -p 27017:27017 mongo

# Setup de proyecto
npm install
npm run dev
\`\`\`

## Verificar Setup

\`\`\`bash
node --version   # v20.x.x
npm --version    # 10.x.x
mongosh          # debería conectarse
\`\`\`
```

---

## 🔗 Enlaces Relacionados

- [[CLI-Reference.md|CLI Commands Reference]]
- [[../Node/Node.js.md|Node.js]]
- [[../Tech-Services/MongoDB.md|MongoDB]]
- [[../Git/_README.md|Git]]

---

## 📚 Recursos Oficiales

- [fnm Official](https://fnm.io/)
- [Homebrew Official](https://brew.sh/)
- [Node.js Official](https://nodejs.org/)
- [nvm Alternative](https://github.com/nvm-sh/nvm)

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación con setup, comandos y ejemplos |
