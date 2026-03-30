---
title: "Prompting avanzado para desarrollo de software"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - prompting
  - ia-agents
  - llm
  - desarrollo
  - claude
category: "01 Stack"
subcategory: "IA-Agents"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[OpenCode]]"
  - "[[Skills en Claude Code]]"
---

# Prompting avanzado para desarrollo de software

La calidad del output de un LLM depende directamente de la calidad del prompt. Estas técnicas son la diferencia entre respuestas genéricas y código listo para producción.

---

## Chain of Thought (CoT)

Pedirle al modelo que "piense paso a paso" antes de responder mejora la precisión en tareas complejas, especialmente en razonamiento y debugging.

```
# Sin CoT
"¿Por qué falla este test?"

# Con CoT
"Analiza este test que falla paso a paso:
1. ¿Qué está intentando testear?
2. ¿Qué hace el código actual?
3. ¿En qué punto divergen?
4. ¿Cuál es la causa raíz?
Luego propón la corrección."
```

Alternativa: añadir simplemente "Think step by step before answering" al final del prompt.

---

## Few-shot examples

Mostrar ejemplos del output esperado elimina ambigüedad y establece el estilo deseado:

```
Necesito que escribas tests unitarios para funciones de utilidad.
Usa este formato exacto:

Ejemplo:
```
// Función a testear
function formatPrice(cents: number): string {
  return `$${(cents / 100).toFixed(2)}`;
}

// Test
describe('formatPrice', () => {
  it('formats cents to dollar string', () => {
    expect(formatPrice(1999)).toBe('$19.99');
    expect(formatPrice(0)).toBe('$0.00');
    expect(formatPrice(100)).toBe('$1.00');
  });
});
```

Ahora escribe los tests para esta función:
[pegar función aquí]
```

---

## Role prompting — system prompts efectivos

Definir el rol del asistente ajusta el tono, nivel de detalle y enfoque de las respuestas:

```
System: Eres un senior TypeScript developer especializado en Next.js App Router
y arquitectura de APIs REST. Revisas código con foco en:
- Type safety y correctness
- Performance (evitar N+1, cacheo apropiado)
- Seguridad (validación de inputs, auth)
- Mantenibilidad

Cuando encuentres un problema, explica por qué es un problema antes de proponer la solución.
Prioriza soluciones idiomáticas sobre soluciones "creativas".
```

---

## XML tags para estructurar prompts largos

Claude responde especialmente bien a prompts estructurados con XML. Facilita separar instrucciones, contexto, ejemplos y output esperado:

```xml
<context>
Estoy trabajando en una API REST con Express + TypeScript.
El proyecto usa zod para validación y JWT para auth.
Base de datos: PostgreSQL con Prisma ORM.
</context>

<task>
Crea un middleware de validación genérico que use zod schemas.
Debe funcionar para body, query params y path params.
</task>

<constraints>
- TypeScript estricto
- Manejo de errores con el formato { error: string, details?: ZodError }
- Compatible con el errorHandler global que ya tenemos
</constraints>

<example>
// Uso esperado en las rutas:
router.post('/users', validate({ body: createUserSchema }), createUser);
router.get('/users/:id', validate({ params: userIdSchema }), getUserById);
</example>
```

---

## Dar contexto de código sin pegar todo el proyecto

Estrategias para dar contexto relevante sin saturar el contexto:

### 1. La firma, no la implementación

```
# En lugar de pegar 200 líneas del servicio:
"El servicio UserService tiene estos métodos disponibles:
- findById(id: string): Promise<User | null>
- findByEmail(email: string): Promise<User | null>
- create(data: CreateUserDTO): Promise<User>
- update(id: string, data: UpdateUserDTO): Promise<User>
- delete(id: string): Promise<void>"
```

### 2. Solo el código relevante + comentarios de estructura

```typescript
// src/api/users/route.ts
// Context: Express Router, usa UserService (ver interfaces arriba)
// Problema: la función de abajo falla cuando user.role es undefined

export async function getUserPermissions(req: Request, res: Response) {
  const user = await UserService.findById(req.params.id);
  // ↓ ERROR: Cannot read properties of undefined
  const permissions = ROLE_PERMISSIONS[user.role.toString()];
  res.json({ permissions });
}
```

### 3. Describir la arquitectura, no el código

```
"La app sigue Clean Architecture:
- controllers/: valida HTTP, delega a services
- services/: lógica de negocio, usa repositories
- repositories/: acceso a datos vía Prisma
- domain/: tipos e interfaces puras

Necesito agregar rate limiting por usuario. ¿Dónde va esta lógica?"
```

---

## Prompts para tareas de desarrollo habituales

### Code review

```
Revisa este PR con foco en:
1. Posibles bugs o casos edge no manejados
2. Problemas de seguridad (inyección, exposición de datos)
3. Performance (queries N+1, renders innecesarios)
4. Mantenibilidad

Para cada issue: severidad (critical/warning/suggestion), qué está mal, y cómo corregirlo.

[pegar diff o código]
```

### Debugging

```
Este test falla con el error:
```
[pegar error completo con stack trace]
```

El código que falla:
[pegar código]

Ya intenté: [qué ya probaste para evitar sugerencias redundantes]
```

### Documentación técnica

```
Escribe la documentación para esta función en formato JSDoc.
La audiencia es un desarrollador mid-level que no conoce el proyecto.
Incluye: propósito, params con tipos, return value, y un ejemplo de uso real.

[pegar función]
```

---

## Errores comunes en prompting

> [!WARNING]
> **Ser demasiado permisivo:** "Hazlo como prefieras" genera output inconsistente. Define el formato y el nivel de detalle que necesitas.
>
> **No especificar el contexto tecnológico:** "Añade autenticación" sin decir qué framework, qué librería de auth, ni qué tipo de app produce soluciones genéricas que probablemente no encajan.
>
> **Pedir múltiples cosas en un prompt:** "Refactoriza el código, agrega tests, documenta todo y optimiza el performance" — el modelo priorizará algunas sobre otras. Un prompt, una tarea principal.
>
> **Ignorar el output y re-preguntar:** Si el modelo da una solución que no te gusta, explica específicamente por qué no sirve en lugar de pedir "otra opción".

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
