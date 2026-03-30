---
title: "Skills en Claude Code — Sistema de comandos personalizados"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - claude-code
  - skills
  - ia-skills
  - automatizacion
  - prompting
category: "01 Stack"
subcategory: "IA-Skills"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Prompting avanzado]]"
  - "[[OpenCode]]"
---

# Skills en Claude Code — Sistema de comandos personalizados

Los Skills de Claude Code son archivos Markdown que definen comandos reutilizables (`/nombre-del-skill`). Cuando el usuario invoca un skill, Claude Code carga su contenido como instrucciones adicionales y las sigue para completar la tarea.

---

## ¿Para qué sirven los Skills?

Los Skills resuelven el problema de tener que repetir instrucciones complejas en cada conversación. En lugar de escribir cada vez "cuando hagas un commit, sigue estas convenciones y verifica esto y aquello...", creas un skill `/commit` que contiene toda esa lógica.

Casos de uso típicos:
- Flujos de trabajo repetitivos (commit, PR, review)
- Generación de documentos con formato específico (DOCX, PPTX)
- Tareas de diseño con restricciones específicas
- Procesos de QA o checklist de verificación

---

## Cómo el agente detecta y aplica un skill

1. El usuario escribe `/nombre-skill` en el prompt
2. Claude Code busca el archivo del skill en `~/.claude/skills/` o en `.claude/skills/` del proyecto
3. Carga el contenido del skill como parte del prompt del sistema
4. Sigue las instrucciones del skill para completar la tarea del usuario

El skill puede incluir:
- Triggers: cuándo activarse automáticamente
- Instrucciones paso a paso
- Formato de output esperado
- Herramientas que debe usar

---

## Estructura de un SKILL.md efectivo

```markdown
# Nombre del Skill

Descripción breve de una línea de qué hace este skill.

## Cuándo se activa

- Cuando el usuario invoca `/nombre-skill`
- [O triggers automáticos si aplica]

## Lo que hace

Descripción de alto nivel de las acciones que tomará Claude al ejecutar este skill.

## Instrucciones paso a paso

1. Primer paso concreto y específico
2. Segundo paso, con condiciones si aplica
   - Si X, entonces hacer Y
   - Si no, hacer Z
3. Tercer paso...

## Formato de output

[Describir exactamente cómo debe ser el resultado]

## Restricciones

- Lo que NO debe hacer
- Límites y condiciones de borde
```

---

## Ejemplo real: Skill de commit

```markdown
# /commit — Crear commit siguiendo Conventional Commits

Crea un commit con mensaje semántico bien formado después de revisar los cambios.

## Instrucciones

1. Ejecuta `git diff --staged` para ver los cambios en staging
2. Si no hay nada en staging, ejecuta `git status` y pregunta al usuario qué agregar
3. Analiza los cambios y determina:
   - Tipo: feat | fix | docs | style | refactor | test | chore | perf | ci
   - Scope: nombre del módulo o área afectada (opcional)
   - Si hay breaking changes
4. Redacta el mensaje en el formato: `tipo(scope): descripción en imperativo`
   - La descripción debe ser en inglés, imperativo presente, sin punto final
   - Máximo 72 caracteres en la primera línea
5. Si los cambios son complejos, añade cuerpo explicando el "por qué"
6. Ejecuta el commit con el mensaje redactado
7. Muestra al usuario el mensaje usado y el hash del commit

## Restricciones

- Nunca usar `--no-verify`
- No hacer commit de archivos .env o que contengan secrets
- Si hay más de 5 archivos modificados con cambios no relacionados, sugerir dividir en commits separados
```

---

## Ejemplo real: Skill de frontend-design

```markdown
# /frontend-design — Revisar y mejorar componentes UI

Revisa el componente con foco en UX, accesibilidad y consistencia visual.

## Cuándo se activa

Cuando el usuario invoca `/frontend-design` seguido de un archivo o descripción.

## Checklist de revisión

1. **Accesibilidad**
   - ¿Los elementos interactivos tienen labels descriptivos?
   - ¿El contraste de color cumple WCAG AA (4.5:1 para texto)?
   - ¿Funciona con navegación por teclado?

2. **Responsive**
   - ¿Se ve bien en móvil (320px) y desktop (1440px)?
   - ¿Los touch targets son de al menos 44x44px?

3. **Estados**
   - ¿Hay estado de loading, error y vacío?
   - ¿Los botones tienen estado disabled cuando corresponde?

4. **Consistencia**
   - ¿Usa las variables de color y spacing del design system?
   - ¿Los textos usan la escala tipográfica definida?

## Output

Devuelve el componente mejorado con comentarios en los cambios realizados,
más una lista de los issues encontrados con su severidad.
```

---

## Cómo crear tu propio skill desde cero

### Paso 1: Identificar la tarea repetitiva

Pregúntate: ¿Qué instrucciones escribo más de una vez a Claude? ¿Qué proceso necesita siempre el mismo checklist o formato?

### Paso 2: Crear el archivo

```bash
# Skills globales (disponibles en todos los proyectos)
mkdir -p ~/.claude/skills
touch ~/.claude/skills/mi-skill.md

# Skills de proyecto (solo en este repo)
mkdir -p .claude/skills
touch .claude/skills/mi-skill.md
```

### Paso 3: Escribir el contenido

Empieza con el flujo feliz: ¿cuáles son los pasos en el caso normal? Luego añade condiciones para los casos edge. Sé específico sobre el formato de output.

### Paso 4: Probar con casos reales

```
/mi-skill [caso de prueba 1]
/mi-skill [caso edge]
```

Ajusta las instrucciones según el output que obtienes.

---

## Skills que vale la pena tener

| Skill | Para qué |
|-------|---------|
| `/commit` | Conventional commits con revisión automática |
| `/pr` | Crear PR con descripción completa |
| `/review` | Code review estructurado |
| `/test` | Generar tests para el archivo actual |
| `/doc` | Generar JSDoc / documentación |
| `/changelog` | Generar CHANGELOG desde los commits |
| `/refactor` | Refactoring con criterios específicos del proyecto |

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
