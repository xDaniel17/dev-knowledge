---
title: "Separación de Responsabilidades — Arquitectura en capas"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - separacion-de-responsabilidades
  - arquitectura
  - capas
  - clean-architecture
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Principios SOLID]]"
  - "[[Middlewares]]"
  - "[[Estructura de proyecto]]"
  - "[[App Router]]"
---

# Separación de Responsabilidades — Arquitectura en capas

La Separación de Responsabilidades (SoC, Separation of Concerns) es el principio de dividir un programa en partes distintas, donde cada parte se ocupa de una preocupación específica. En práctica: la validación no mezcla con la lógica de negocio, que no mezcla con el acceso a datos.

---

## El problema de mezclar todo

```typescript
// ❌ Todo mezclado en el handler — difícil de testear, mantener y reutilizar
app.post('/api/users', async (req, res) => {
  // Validación
  if (!req.body.email || !req.body.email.includes('@')) {
    return res.status(400).json({ error: 'Email inválido' });
  }

  // Verificar si existe (lógica de negocio + acceso a datos mezclados)
  const existing = await db.user.findUnique({ where: { email: req.body.email } });
  if (existing) return res.status(409).json({ error: 'Email en uso' });

  // Hashear contraseña
  const hash = await bcrypt.hash(req.body.password, 10);

  // Crear usuario
  const user = await db.user.create({
    data: { email: req.body.email, password: hash }
  });

  // Enviar email de bienvenida
  await mailer.send({ to: user.email, template: 'welcome' });

  res.status(201).json({ id: user.id, email: user.email });
});
```

Problemas: no puedes testear la lógica sin levantar HTTP. No puedes reutilizar la lógica de creación de usuario. Cambiar la BD o el mailer implica tocar el handler.

---

## La solución: capas con responsabilidad única

```
HTTP Request
     │
     ▼
┌─────────────────┐   Validar inputs HTTP, transformar a DTOs,
│   Controller    │   devolver respuestas HTTP. Sin lógica de negocio.
└────────┬────────┘
         │ DTO limpio
         ▼
┌─────────────────┐   Reglas de negocio: ¿puede crearse el usuario?
│    Service      │   Orquestar operaciones. Sin saber de HTTP ni BD específica.
└────────┬────────┘
         │ entidades de dominio
         ▼
┌─────────────────┐   Acceso a datos: SQL, ORM, caché.
│  Repository     │   Sin lógica de negocio.
└────────┬────────┘
         │
         ▼
     Base de Datos
```

---

## Implementación en Express + TypeScript

```typescript
// ─── Controller ────────────────────────────────────────────────────
// Solo sabe de HTTP: parsear request, delegar, formatear response
export async function createUserController(req: Request, res: Response, next: NextFunction) {
  try {
    const dto = CreateUserSchema.parse(req.body); // validación con zod
    const user = await userService.create(dto);
    res.status(201).json({ id: user.id, email: user.email });
  } catch (err) {
    next(err); // delegar manejo de errores al middleware global
  }
}

// ─── Service ────────────────────────────────────────────────────────
// Contiene las reglas de negocio. No importa si viene de HTTP o de un cron job.
export class UserService {
  constructor(
    private repo: UserRepository,
    private mailer: MailService,
  ) {}

  async create(dto: CreateUserDTO): Promise<User> {
    const existing = await this.repo.findByEmail(dto.email);
    if (existing) throw new ConflictError('Email ya registrado');

    const hashed = await hash(dto.password, 10);
    const user = await this.repo.save({ email: dto.email, password: hashed });

    await this.mailer.sendWelcome(user.email); // efecto secundario separado

    return user;
  }
}

// ─── Repository ─────────────────────────────────────────────────────
// Solo sabe de la BD. No conoce las reglas de negocio.
export class PrismaUserRepository implements UserRepository {
  async findByEmail(email: string): Promise<User | null> {
    return prisma.user.findUnique({ where: { email } });
  }

  async save(data: { email: string; password: string }): Promise<User> {
    return prisma.user.create({ data });
  }
}
```

Ahora puedes testear `UserService` con un mock de `UserRepository` sin tocar la BD. Puedes cambiar de Prisma a otro ORM sin tocar el Service. Puedes llamar `userService.create()` desde un endpoint HTTP, un job, o una CLI.

---

## SoC en el frontend — componentes, hooks y estado

En React, la separación se aplica entre presentación, lógica y estado:

```tsx
// ─── Lógica de datos (hook) ─────────────────────────────────────────
function useUsers() {
  const [users, setUsers] = useState<User[]>([]);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetch('/api/users')
      .then(r => r.json())
      .then(setUsers)
      .catch(e => setError(e.message))
      .finally(() => setLoading(false));
  }, []);

  return { users, loading, error };
}

// ─── Presentación (componente) ──────────────────────────────────────
// Solo sabe de UI — no sabe de fetch, de la API, ni del estado global
function UserList({ users, loading, error }: UserListProps) {
  if (loading) return <Spinner />;
  if (error) return <ErrorMessage message={error} />;
  return (
    <ul>
      {users.map(user => <UserCard key={user.id} user={user} />)}
    </ul>
  );
}

// ─── Composición ────────────────────────────────────────────────────
function UsersPage() {
  const { users, loading, error } = useUsers();
  return <UserList users={users} loading={loading} error={error} />;
}
```

`UserList` puede testearse con props estáticas sin hacer fetch. `useUsers` puede testearse sin montar componentes. Los cambios en la UI no afectan la lógica de datos.

---

## SoC en Next.js App Router

Next.js App Router refuerza SoC a nivel de framework:

| Capa | Responsabilidad | Cómo |
|------|----------------|------|
| `page.tsx` | Composición de la ruta | Server Component — orquesta |
| `loading.tsx` | Estado de carga | Automático con Suspense |
| `error.tsx` | Manejo de errores | Error Boundary automático |
| Server Actions | Mutaciones | Solo lógica de servidor |
| Client Components | Interactividad | `'use client'` |

---

## La trampa del over-engineering

> [!WARNING]
> SoC no significa "crear una carpeta y un archivo para cada concepto imaginable". El principio se aplica cuando la mezcla causa un problema real: difícil de testear, de cambiar, o de entender.
>
> Para un CRUD simple con 3 endpoints, un controller que llama directo a Prisma puede ser suficiente. La arquitectura en capas paga cuando el dominio crece y la lógica de negocio se vuelve compleja.
>
> Empieza simple. Separa cuando duela.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
