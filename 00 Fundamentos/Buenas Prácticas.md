---
title: "Buenas Prácticas — DRY, KISS, YAGNI, naming, error handling"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - buenas-practicas
  - dry
  - kiss
  - yagni
  - naming
  - error-handling
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Principios SOLID]]"
  - "[[Separación de Responsabilidades]]"
  - "[[Asincronía]]"
  - "[[Middlewares]]"
  - "[[Flujo de trabajo]]"
---

# Buenas Prácticas — DRY, KISS, YAGNI, naming, error handling

Las buenas prácticas son heurísticas que hacen el código más legible, mantenible y robusto. No son reglas absolutas — son guías para tomar mejores decisiones bajo incertidumbre.

---

## DRY — Don't Repeat Yourself

> "Cada pieza de conocimiento debe tener una representación única, inequívoca y autoritativa en el sistema."
> — Andrew Hunt & Dave Thomas, *The Pragmatic Programmer*

DRY no se trata de no copiar líneas de código — se trata de no duplicar **conocimiento**. Si cambias una regla de negocio y tienes que actualizar tres archivos, tienes un problema de DRY.

```typescript
// ❌ Duplicación de conocimiento — la validación del email se repite
function createUser(email: string) {
  if (!email.includes('@') || email.length < 5) throw new Error('Email inválido');
  // ...
}

function updateUserEmail(userId: string, email: string) {
  if (!email.includes('@') || email.length < 5) throw new Error('Email inválido'); // ← duplicado
  // ...
}

// ✅ Un único lugar de verdad
const emailSchema = z.string().email().min(5);

function validateEmail(email: string): string {
  return emailSchema.parse(email); // lanza si es inválido
}

function createUser(email: string) {
  validateEmail(email);
  // ...
}

function updateUserEmail(userId: string, email: string) {
  validateEmail(email); // misma lógica, un solo lugar
  // ...
}
```

> [!WARNING]
> **DRY mal aplicado crea abstracciones prematuras.** Dos funciones con código similar no siempre representan el mismo conocimiento. Si dos partes del sistema evolucionan de forma independiente, duplicar puede ser correcto. La regla de tres: duplícalo una vez, a la tercera abstrae.

---

## KISS — Keep It Simple, Stupid

La solución más simple que funciona es la correcta. La complejidad innecesaria es deuda técnica pagada con intereses.

```typescript
// ❌ Over-engineered para un caso simple
class UserEmailValidator {
  private static instance: UserEmailValidator;
  private validationStrategy: ValidationStrategy;

  private constructor() {
    this.validationStrategy = new RegexEmailValidationStrategy();
  }

  static getInstance() { ... }
  setStrategy(s: ValidationStrategy) { ... }
  validate(email: string): boolean { ... }
}

// ✅ KISS — una función hace el trabajo
function isValidEmail(email: string): boolean {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
}
```

**Señales de que estás violando KISS:**
- Necesitas explicar tu código con un diagrama antes de que alguien lo entienda
- Un `if` simple se reemplazó por tres clases y una interface
- La función resuelve casos hipotéticos que probablemente nunca van a ocurrir

---

## YAGNI — You Aren't Gonna Need It

No implementes funcionalidad antes de que sea necesaria. El código que no existe no tiene bugs, no necesita tests y no hay que mantenerlo.

```typescript
// ❌ YAGNI violation — implementando para el futuro imaginado
interface PaymentProcessor {
  charge(amount: number): Promise<void>;
  refund(transactionId: string): Promise<void>;
  partialRefund(transactionId: string, amount: number): Promise<void>;
  schedulePayment(amount: number, date: Date): Promise<void>;
  createSubscription(planId: string): Promise<void>;
  pauseSubscription(subscriptionId: string): Promise<void>;
  // ... 15 métodos más que "podrías necesitar"
}

// ✅ YAGNI — implementa solo lo que necesitas ahora
interface PaymentProcessor {
  charge(amount: number): Promise<void>;
}
```

YAGNI y DRY no se contradicen: DRY dice "no repitas lo que ya existe"; YAGNI dice "no construyas lo que aún no necesitas".

---

## Naming — nombrar bien es la mitad del trabajo

El buen naming hace el código autodocumentado. Si necesitas un comentario para explicar qué hace una variable, probablemente el nombre es malo.

### Variables y funciones

```typescript
// ❌ Nombres que no dicen nada
const d = new Date();
const arr = users.filter(u => u.a);
function proc(x: any) { ... }

// ✅ Nombres que comunican intención
const createdAt = new Date();
const activeUsers = users.filter(u => u.isActive);
function sendWelcomeEmail(user: User): Promise<void> { ... }
```

### Booleanos — siempre con prefijo verbal

```typescript
// ❌
const admin = user.role === 'admin';
const modal = true;
const data = !!response;

// ✅
const isAdmin = user.role === 'admin';
const isModalOpen = true;
const hasData = !!response;
const canEdit = isAdmin || isOwner;
const shouldRefetch = staleness > THRESHOLD;
```

### Funciones — verbo + sustantivo

```typescript
// ❌ nombres de sustantivo (parece una variable)
function userCreation(data: CreateUserDTO) { ... }
function emailValidation(email: string) { ... }

// ✅ verbo + sustantivo (acción clara)
function createUser(data: CreateUserDTO) { ... }
function validateEmail(email: string) { ... }
function fetchUserById(id: string) { ... }
function sendPasswordResetEmail(email: string) { ... }
```

### Evitar abreviaturas no estándar

```typescript
// ❌
const usr = await getUsr(usrId);
const cfg = loadCfg();
const btn = document.querySelector('.btn');

// ✅ Las abreviaturas estándar (id, url, dto, i, j) están bien
const user = await getUser(userId);
const config = loadConfig();
const submitButton = document.querySelector('.submit-btn');
```

---

## Error Handling — fallar de forma explícita y útil

### Tres tipos de errores

```typescript
// 1. Errores de dominio — condiciones esperadas del negocio
class NotFoundError extends Error {
  constructor(resource: string, id: string) {
    super(`${resource} with id "${id}" not found`);
    this.name = 'NotFoundError';
  }
}

class ConflictError extends Error {
  constructor(message: string) {
    super(message);
    this.name = 'ConflictError';
  }
}

// 2. Errores de validación — inputs incorrectos
class ValidationError extends Error {
  constructor(public fields: Record<string, string>) {
    super('Validation failed');
    this.name = 'ValidationError';
  }
}

// 3. Errores inesperados — bugs, fallos de infraestructura
// No los tipifiques — solo loguéalos y propaga
```

### Manejo en capas

```typescript
// Service — lanza errores de dominio
async function getUser(id: string): Promise<User> {
  const user = await userRepo.findById(id);
  if (!user) throw new NotFoundError('User', id);
  return user;
}

// Controller — convierte errores de dominio a respuestas HTTP
async function getUserController(req: Request, res: Response, next: NextFunction) {
  try {
    const user = await getUser(req.params.id);
    res.json(user);
  } catch (err) {
    next(err); // delegar al error handler global
  }
}

// Error handler global — un solo lugar de conversión a HTTP
app.use((err: Error, req: Request, res: Response, next: NextFunction) => {
  if (err instanceof NotFoundError)  return res.status(404).json({ error: err.message });
  if (err instanceof ConflictError)  return res.status(409).json({ error: err.message });
  if (err instanceof ValidationError) return res.status(400).json({ error: err.message, fields: err.fields });

  // Error inesperado — no exponer detalles al cliente
  console.error(err);
  res.status(500).json({ error: 'Internal server error' });
});
```

### No silencies errores

```typescript
// ❌ El error desaparece silenciosamente
try {
  await sendEmail(user.email);
} catch (e) {} // ← nunca hagas esto

// ✅ Al menos loguea
try {
  await sendEmail(user.email);
} catch (e) {
  logger.error('Failed to send welcome email', { userId: user.id, error: e });
  // Decidir: ¿es crítico? ¿lanzar o continuar?
}
```

---

## Principio de menor sorpresa

Una función debe hacer lo que su nombre sugiere — nada más, nada menos.

```typescript
// ❌ Sorpresa: getUser modifica la BD
async function getUser(id: string) {
  const user = await db.user.findUnique({ where: { id } });
  await db.userActivity.create({ data: { userId: id, action: 'viewed' } }); // ← efecto secundario oculto
  return user;
}

// ✅ Responsabilidades separadas y explícitas
async function getUser(id: string) {
  return db.user.findUnique({ where: { id } });
}

async function recordUserView(userId: string) {
  return db.userActivity.create({ data: { userId, action: 'viewed' } });
}
```

---

## Funciones pequeñas y enfocadas

Una función debe hacer una sola cosa. Si necesitas usar "y" para describir qué hace, probablemente debería ser dos funciones.

```typescript
// ❌ "valida el formulario Y envía los datos Y muestra el mensaje de éxito"
async function handleFormSubmit(formData: FormData) {
  // 60 líneas de validación, fetch, y manipulación del DOM
}

// ✅ Una responsabilidad por función
function validateForm(data: FormData): ValidationResult { ... }
async function submitToAPI(data: ValidatedData): Promise<APIResponse> { ... }
function showSuccessMessage(message: string): void { ... }

async function handleFormSubmit(formData: FormData) {
  const validation = validateForm(formData);
  if (!validation.valid) return showErrors(validation.errors);

  const response = await submitToAPI(validation.data);
  showSuccessMessage(response.message);
}
```

---

## Comentarios — cuándo y cuándo no

```typescript
// ❌ Comentario que solo repite el código
// Incrementa el contador en 1
counter++;

// ❌ Comentario de código "comentado" — usar git para esto
// const oldImpl = doSomething();

// ✅ Comentario que explica el POR QUÉ, no el QUÉ
// Prisma no actualiza relaciones anidadas automáticamente — hay que hacerlo en dos pasos
await db.post.update({ where: { id }, data: { title } });
await db.post.update({ where: { id }, data: { tags: { set: newTags } } });

// ✅ Comentario para contexto no obvio
// El delay de 100ms evita el race condition con la animación de cierre del modal
await new Promise(resolve => setTimeout(resolve, 100));
await cleanup();
```

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
