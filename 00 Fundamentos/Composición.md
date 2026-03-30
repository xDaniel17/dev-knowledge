---
title: "Composición — Construir con piezas pequeñas"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - composicion
  - funcional
  - patrones
  - fundamentos
category: "00 Fundamentos"
subcategory: ""
author: Daniel
version: 1.0.0
status: active
related:
  - "[[Middlewares]]"
  - "[[useState]]"
  - "[[Signals]]"
  - "[[Principios SOLID]]"
---

# Composición — Construir con piezas pequeñas

Composición es el principio de construir sistemas complejos combinando piezas simples y autónomas. Es la alternativa a la herencia como mecanismo principal de reutilización, y es el patrón dominante en el frontend moderno y en Node/Express.

---

## Composición vs Herencia

La herencia dice "esto ES un tipo de aquello". La composición dice "esto TIENE estas capacidades".

```js
// Herencia — acoplamiento rígido
class Animal {
  eat() { ... }
  sleep() { ... }
}

class Dog extends Animal {
  bark() { ... }
}

class RobotDog extends Animal { // ¿un robot come y duerme? problema
  bark() { ... }
}

// ✅ Composición — flexible, combina comportamientos
const canEat    = (state) => ({ eat:   () => `${state.name} eat`   });
const canSleep  = (state) => ({ sleep: () => `${state.name} sleep` });
const canBark   = (state) => ({ bark:  () => `${state.name} bark`  });

const createDog      = (name) => ({ ...canEat({ name }), ...canSleep({ name }), ...canBark({ name }) });
const createRobotDog = (name) => ({ ...canBark({ name }) }); // solo lo que necesita
```

El lema popular: **"Favorecer la composición sobre la herencia"** (GoF, 1994).

---

## Composición en React — componentes y hooks

React está construido sobre composición. Un componente complejo no hereda de uno base — combina componentes más pequeños:

```tsx
// Piezas pequeñas y reutilizables
function Avatar({ src, alt }: AvatarProps) {
  return <img src={src} alt={alt} className="rounded-full w-10 h-10" />;
}

function Badge({ count }: { count: number }) {
  return <span className="badge">{count}</span>;
}

function UserName({ name, role }: { name: string; role: string }) {
  return (
    <div>
      <p className="font-semibold">{name}</p>
      <p className="text-sm text-gray-500">{role}</p>
    </div>
  );
}

// Componer piezas en un componente más complejo
function UserCard({ user, notifications }: UserCardProps) {
  return (
    <div className="flex items-center gap-3">
      <div className="relative">
        <Avatar src={user.avatarUrl} alt={user.name} />
        {notifications > 0 && <Badge count={notifications} />}
      </div>
      <UserName name={user.name} role={user.role} />
    </div>
  );
}
```

### Custom Hooks — composición de lógica

Los hooks también se componen: puedes crear hooks que usan otros hooks:

```tsx
// Hook de bajo nivel
function useLocalStorage<T>(key: string, initial: T) {
  const [value, setValue] = useState<T>(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initial;
  });

  const set = (newValue: T) => {
    setValue(newValue);
    localStorage.setItem(key, JSON.stringify(newValue));
  };

  return [value, set] as const;
}

// Hook de alto nivel que compone el anterior
function useTheme() {
  const [theme, setTheme] = useLocalStorage<'light' | 'dark'>('theme', 'light');

  const toggle = () => setTheme(theme === 'light' ? 'dark' : 'light');

  return { theme, toggle };
}

// Uso
function ThemeButton() {
  const { theme, toggle } = useTheme();
  return <button onClick={toggle}>{theme === 'light' ? '🌙' : '☀️'}</button>;
}
```

---

## Composición en Express — middleware pipeline

El sistema de middlewares de Express es composición de funciones puras. Cada middleware hace una cosa y pasa el control:

```js
// Piezas pequeñas
const withJson       = express.json();
const withLogging    = requestLogger;
const withAuth       = requireAuth;
const withRateLimit  = rateLimit({ windowMs: 15 * 60 * 1000, max: 100 });

// Componer para una ruta específica
app.post('/api/sensitive',
  withJson,       // parsear body
  withLogging,    // registrar la petición
  withRateLimit,  // limitar frecuencia
  withAuth,       // verificar JWT
  sensitiveHandler // lógica de negocio
);
```

El orden importa — cada pieza recibe lo que la anterior dejó en `req/res`.

---

## Composición de funciones — el patrón funcional

En programación funcional, la composición es una operación formal: `f(g(x))`.

```js
// Funciones pequeñas y puras
const trim     = str => str.trim();
const lower    = str => str.toLowerCase();
const slugify  = str => str.replace(/\s+/g, '-').replace(/[^a-z0-9-]/g, '');
const truncate = (n) => str => str.slice(0, n);

// Componer manualmente
const toSlug = str => slugify(lower(trim(str)));

// O con una función compose/pipe
const pipe = (...fns) => x => fns.reduce((v, f) => f(v), x);

const toSlug = pipe(trim, lower, slugify);
const toShortSlug = pipe(trim, lower, slugify, truncate(50));

toSlug('  Hello World!  '); // 'hello-world'
```

---

## Señales y composición reactiva

Angular `computed()` es composición reactiva: un valor derivado de múltiples señales fuente:

```typescript
const firstName = signal('Daniel');
const lastName  = signal('Herrera');
const age       = signal(30);

// Componer múltiples signals en uno derivado
const fullName    = computed(() => `${firstName()} ${lastName()}`);
const displayName = computed(() => `${fullName()} (${age()})`);

firstName.set('Carlos');
// fullName y displayName se actualizan automáticamente
```

---

## Principio: pequeño, enfocado, componible

Una pieza componible tiene estas características:

1. **Hace una sola cosa** — fácil de entender y testear aislada
2. **No tiene efectos secundarios inesperados** — predecible
3. **Recibe inputs explícitos, produce outputs explícitos** — sin dependencias ocultas
4. **No asume contexto externo** — funciona sin saber quién la llama

Cuando una función o componente se vuelve difícil de reutilizar, suele ser porque viola alguno de estos puntos.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
