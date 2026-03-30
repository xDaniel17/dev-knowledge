---
title: "OpenCode — Agente de terminal con IA"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - opencode
  - ia-tools
  - terminal
  - agente-ia
  - desarrollo
category: "01 Stack"
subcategory: "IA-Tools"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Skills en Claude Code]]"
  - "[[Prompting avanzado]]"
---

# OpenCode — Agente de terminal con IA

OpenCode es un agente de IA que vive en la terminal. Entiende el contexto de tu repositorio y ejecuta tareas de desarrollo directamente: leer archivos, escribir código, correr comandos, crear PRs. Funciona con múltiples proveedores de IA incluyendo Claude de Anthropic.

---

## Instalación y configuración inicial

```bash
# Instalar via npm (global)
npm install -g opencode

# O via brew (macOS)
brew install opencode

# Verificar instalación
opencode --version

# Iniciar en el directorio de tu proyecto
cd mi-proyecto
opencode
```

Al iniciar por primera vez, OpenCode te pedirá configurar un proveedor de IA:

```bash
# Configurar Claude como proveedor
opencode config set provider anthropic
opencode config set model claude-sonnet-4-6

# O con OpenAI
opencode config set provider openai
opencode config set model gpt-4o
```

---

## Cómo funciona

OpenCode indexa tu repositorio al iniciar: lee la estructura de carpetas, detecta el stack tecnológico (package.json, tsconfig, etc.) y construye un contexto. Desde ahí, puedes darle instrucciones en lenguaje natural y ejecutará las acciones necesarias:

1. Lee archivos relevantes para entender el contexto
2. Planifica los cambios
3. Escribe o modifica código
4. Ejecuta comandos si es necesario (tests, build, lint)
5. Informa el resultado

```
you → [instrucción en lenguaje natural]
         ↓
opencode → [lee contexto] → [planifica] → [ejecuta herramientas] → [responde]
```

---

## Prompts efectivos para OpenCode

La calidad del output depende mucho de cómo formulas el prompt.

### Ser específico con el contexto

```
# ❌ Vago
"Arregla el bug"

# ✅ Con contexto completo
"En src/api/users/route.ts, el endpoint GET /api/users está devolviendo un 500
cuando el parámetro 'role' es undefined. El error es 'Cannot read properties of
undefined (reading toString)'. Arréglalo validando el parámetro antes de usarlo."
```

### Dar el estado deseado, no los pasos

```
# ❌ Instruir cada paso
"Abre el archivo X, busca la función Y, añade una validación Z en la línea 42..."

# ✅ Describir el resultado esperado
"El formulario de registro debe mostrar un mensaje de error en tiempo real
cuando el email ya existe en la base de datos. Usa el endpoint /api/check-email
que ya existe."
```

### Para refactoring

```
"Refactoriza los archivos en src/components/ que usan class components de React
a functional components con hooks. Empieza por los más simples. Mantén el mismo
comportamiento externo — no cambies las props ni los nombres de los componentes."
```

---

## Integración con Claude como backend

```bash
# Configurar con API key de Anthropic
export ANTHROPIC_API_KEY="sk-ant-..."

# O en el archivo de configuración
~/.config/opencode/config.json:
{
  "provider": "anthropic",
  "model": "claude-sonnet-4-6",
  "apiKey": "sk-ant-..."
}
```

OpenCode envía a Claude:
- El prompt del usuario
- Contexto del repositorio (estructura, archivos relevantes)
- Historial de la conversación actual
- Resultados de las herramientas ejecutadas

---

## Diferencias con Claude Code y Cursor

| Característica | OpenCode | Claude Code | Cursor |
|---|---|---|---|
| Interface | Terminal TUI | Terminal CLI | IDE completo |
| Portabilidad | Alta — cualquier terminal | Alta — CLI | Baja — requiere el IDE |
| Integración IDE | No | No | Nativa |
| Proveedor IA | Múltiples | Claude (Anthropic) | Claude + GPT-4 |
| Offline | No | No | Parcial |
| Contexto del repo | Automático | Manual/herramientas | Automático |
| Precio | Open source | Suscripción Anthropic | Suscripción Cursor |

---

## Casos de uso donde brilla OpenCode

1. **Servidores remotos / SSH:** cuando no puedes abrir un IDE, pero sí una terminal
2. **CI/CD pipelines:** automatizar revisiones de código o generación de documentación
3. **Proyectos multi-repo:** cambiar de contexto rápidamente entre terminales
4. **Scripting y automatización:** integrarlo en scripts bash para tareas repetitivas
5. **Equipos con restricciones de IDE:** entornos donde no se puede instalar software adicional

---

## Tips de uso

```bash
# Dar contexto adicional al iniciar
opencode --context "Este es un proyecto Next.js con Strapi como backend.
Usamos TypeScript strict y Tailwind para estilos."

# Modo no interactivo (para scripting)
opencode --no-interactive "Genera tests unitarios para src/utils/formatDate.ts"

# Ver el contexto que OpenCode tiene del repo
opencode context show
```

> [!TIP]
> Al iniciar una sesión de trabajo compleja, dedica el primer mensaje a describir el contexto: qué estás construyendo, qué restricciones existen, y qué acabas de hacer. OpenCode no recuerda conversaciones anteriores entre sesiones.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
