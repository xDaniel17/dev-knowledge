---
title: GitHub Copilot
date: 2026-03-29
lastUpdated: 2026-03-29
tags: 
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: ["Claude-Code", "Agentes-IA", "OpenCode", "01-Conceptos/README"]
migrated: true
migratedFrom: 01 Stack/IA-Tools/GitHub-Copilot.md
---

# GitHub Copilot

GitHub Copilot es un **asistente de código impulsado por IA** integrado directamente en editores populares (VS Code, JetBrains, Vim, etc.). A diferencia de los agentes, actúa como **autocomplete inteligente**.

## 🎯 Características principales

- **Autocomplete Inteligente**: Sugiere líneas completas o funciones
- **Multi-editor**: VS Code, JetBrains (IntelliJ, PyCharm), Neovim, Vim
- **Generación de código**: Crea funciones basadas en comentarios
- **Explicación de código**: Entiende y explica código existente
- **Tests**: Generar pruebas unitarias automáticamente
- **Snippets**: Fragmentos de código contextuales
- **Cheap**: Más económico que otros servicios IA ($10/mes o $100/año)
- **Enterprise**: Versión empresarial con soporte
- **Chat**: Interfaz de chat integrada (no agente completo)

## 📊 Diferencias: Copilot vs Agentes

| Característica | Copilot | Claude Code | OpenCode |
|---|---|---|---|
| **Tipo** | Autocomplete | Agente CLI | Agente CLI |
| **Interfaz** | Editor | Terminal | Terminal |
| **Autonomía** | Baja (sugiere) | Alta (ejecuta) | Alta (ejecuta) |
| **Acceso a archivos** | Archivo actual | Proyecto completo | Proyecto completo |
| **Iteración** | Manual | Automática | Automática |
| **Costo** | $10/mes | Paga por uso | Flexible (múltiples proveedores) |
| **Multi-proveedor** | No (OpenAI) | No (Anthropic) | Sí |

## 🔧 Instalación

### VS Code

1. Abre VS Code
2. Ir a Extensions (Ctrl+Shift+X)
3. Buscar "GitHub Copilot"
4. Click Install
5. Sign in con GitHub
6. Autorizar extensión

### JetBrains (IntelliJ, PyCharm, etc.)

1. IDE → Preferences/Settings
2. Plugins → Marketplace
3. Buscar "GitHub Copilot"
4. Click Install
5. Reiniciar IDE
6. Sign in con GitHub

### Vim/Neovim

```bash
# Usando vim-plug
Plug 'github/copilot.vim'

# Usando packer.nvim
use 'github/copilot.vim'
```

```bash
# Dentro de Vim/Neovim
:Copilot setup
```

### Configurar licencia

1. Ir a https://github.com/settings/copilot
2. Seleccionar plan:
   - **Copilot** ($10/mes)
   - **Copilot Pro** ($20/mes, incluye Copilot Chat con GPT-4)
   - **Copilot Business** (empresa)
   - **Copilot Enterprise** (enterprise custom)

## 💡 Casos de uso

### Autocomplete de código

Escribe una función parcialmente:
```javascript
function calculateTotal(
```

Copilot sugiere automáticamente la implementación.

### Generar código desde comentarios

```python
# Función que valida si un email es válido
def validate_email(email):
```

Copilot completa la función.

### Generar tests

```javascript
// generateTestCases completes with test suite
test('should validate email', () => {
```

### Refactor de código

Selecciona código y pide en chat:
```
Refactor este código para ser más eficiente
```

### Explicación

Selecciona código problemático:
```
/explain
```

Copilot explica qué hace.

## 🎮 Atajos de teclado

| Acción | Atajo |
|--------|-------|
| **Aceptar sugerencia** | Tab |
| **Siguiente sugerencia** | Alt+] (VS Code) |
| **Anterior sugerencia** | Alt+[ (VS Code) |
| **Rechazar** | Esc |
| **Abrir Copilot Chat** | Ctrl+Shift+I |
| **Crear Pull Request** | /pr |

## 💬 Copilot Chat

### Disponible en

- VS Code (extensión GitHub Copilot)
- Visual Studio
- JetBrains IDEs
- Aplicación web en github.com

### Ejemplos de prompts

```
/explain - Explicar el código seleccionado
/doc - Generar documentación
/fix - Arreglar el código
/tests - Generar tests
/refactor - Refactorizar código
```

### Chat window

```
@workspace - Referenciar archivos del proyecto
#file - Referenciar archivo específico
```

## ⚙️ Configuración

### VS Code

`settings.json`:
```json
{
  "github.copilot.enable": {
    "*": true,
    "plaintext": false,
    "markdown": false
  },
  "github.copilot.advanced": {
    "authProvider": "github"
  }
}
```

### Desactivar en ciertos tipos de archivo

```json
{
  "github.copilot.enable": {
    "*": true,
    "yaml": false,
    "json": false
  }
}
```

## 🔒 Privacidad y seguridad

### Datos enviados a GitHub

- Código visible en el editor
- Contexto del repositorio (no privado)
- Configuración del workspace
- Metadatos de sesión

### GitHub nunca

- Usa tu código para entrenar modelos públicamente
- Comparte código con otros usuarios
- Vende datos

### Enterprise y Business

Opción para no entrenar modelos.

## 📊 Límites y consideraciones

### Limitaciones

- Contexto limitado (no ve proyecto completo)
- Puede generar código inseguro/incorrecto
- Mejor con lenguajes populares (JS, Python, Java)
- No hace debugging automático como agentes
- No ejecuta código

### Fortalezas

- Integración perfecta en editor
- Muy rápido (autocomplete instantáneo)
- Económico ($10/mes)
- Aprendizaje automático continuo
- Comunidad gigante (GitHub)

## 🚀 Copilot vs Agentes: Cuándo usar cada uno

### Usa GitHub Copilot cuando

- Necesitas autocomplete mientras codeas
- Trabajas en un proyecto pequeño
- Presupuesto limitado
- Prefieres interfaz en editor (no CLI)
- Solo necesitas sugerencias (no ejecución)

### Usa Claude Code cuando

- Necesitas un agente que ejecute cambios
- Trabajas en proyectos grandes/complejos
- Quieres debugging automático
- Acceso a proyecto completo/contexto
- Presupuesto más alto

### Usa OpenCode cuando

- Quieres flexibilidad de proveedor LLM
- Necesitas open source
- Prefieres terminal
- Trabajas en múltiples proyectos
- Quieres personalización total

## 🔗 Referencias relacionadas

- [[Agentes-IA]] - Diferencia con agentes
- [[Claude-Code]] - Agente avanzado
- [[OpenCode]] - Agente multi-proveedor
- [[01-Conceptos/README]] - Conceptos fundamentales

## 📚 Recursos externos

- [GitHub Copilot](https://github.com/features/copilot)
- [Copilot Documentation](https://docs.github.com/en/copilot)
- [Copilot Chat](https://github.com/features/copilot/chat)
- [Pricing](https://github.com/features/copilot#pricing)
- [Security & Privacy](https://github.blog/2023-08-01-github-copilot-partnership-with-openai/)

---

## 📋 Historial de versiones

| Versión | Fecha | Cambios |
|---------|-------|---------|
| **1.0.0** | 2026-03-29 | Documentación inicial: características, instalación, uso, comparativas |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
