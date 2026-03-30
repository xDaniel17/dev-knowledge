---
title: "useState — Hook de estado en React"
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - react
  - hooks
  - estado
  - frontend
category: "01 Stack"
subcategory: "React"
author: Daniel
version: 1.0.0
status: active
related:
  - "[[useEffect]]"
  - "[[React Query]]"
  - "[[App Router]]"
---

# useState — Hook de estado en React

`useState` es el hook más fundamental de React. Permite que un componente funcional tenga y actualice su propio estado local, disparando un re-render cuando ese estado cambia.

---

## ¿Qué es useState?

`useState` es una función que recibe un valor inicial y devuelve un array de dos elementos: el estado actual y una función para actualizarlo.

```tsx
const [state, setState] = useState(initialValue);
```

Internamente, React almacena el estado fuera del componente en una estructura de "fiber". Cada vez que llamas `setState`, React programa un re-render del componente con el nuevo valor. El orden de llamada de los hooks debe ser siempre el mismo entre renders — por eso no puedes llamar hooks dentro de condicionales.

---

## Casos de uso comunes

### 1. Contador simple

```tsx
import { useState } from 'react';

function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Contador: {count}</p>
      <button onClick={() => setCount(count + 1)}>+1</button>
      <button onClick={() => setCount(count - 1)}>-1</button>
      <button onClick={() => setCount(0)}>Resetear</button>
    </div>
  );
}
```

### 2. Formulario controlado

```tsx
function LoginForm() {
  const [email, setEmail] = useState('');
  const [password, setPassword] = useState('');

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    console.log({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Contraseña"
      />
      <button type="submit">Ingresar</button>
    </form>
  );
}
```

### 3. Estado complejo con objeto

Cuando el estado es un objeto, nunca mutes el objeto directamente — siempre crea uno nuevo con spread:

```tsx
type UserProfile = {
  name: string;
  email: string;
  role: 'admin' | 'user';
};

function ProfileEditor() {
  const [profile, setProfile] = useState<UserProfile>({
    name: 'Daniel',
    email: 'daniel@example.com',
    role: 'admin',
  });

  const updateName = (name: string) => {
    // CORRECTO: nuevo objeto con spread
    setProfile(prev => ({ ...prev, name }));
  };

  const updateRole = (role: UserProfile['role']) => {
    setProfile(prev => ({ ...prev, role }));
  };

  return (
    <div>
      <input value={profile.name} onChange={(e) => updateName(e.target.value)} />
      <p>Rol: {profile.role}</p>
    </div>
  );
}
```

---

## Actualización basada en estado anterior

Cuando el nuevo estado depende del anterior, usa la forma funcional de `setState`. Esto evita bugs en actualizaciones rápidas o en closures desactualizadas:

```tsx
// ❌ Puede fallar si se llama varias veces seguidas
setCount(count + 1);

// ✅ Siempre usa el valor más reciente
setCount(prev => prev + 1);
```

---

## Estado asíncrono — el bug clásico

`setState` es asíncrono: después de llamarlo, `state` no cambia inmediatamente en la misma ejecución.

```tsx
function Example() {
  const [count, setCount] = useState(0);

  const handleClick = () => {
    setCount(count + 1);
    console.log(count); // ❌ sigue siendo el valor anterior
    // React todavía no re-renderizó
  };
}
```

Si necesitas leer el nuevo valor, hazlo en el siguiente render o usa `useEffect`.

---

## Inicialización costosa — lazy initializer

Si el valor inicial requiere un cálculo costoso, pasa una función en lugar del valor. React la ejecuta solo en el primer render:

```tsx
// ❌ Recalcula localStorage en cada render
const [data, setData] = useState(JSON.parse(localStorage.getItem('data') || '[]'));

// ✅ Solo ejecuta la función en el montaje inicial
const [data, setData] = useState(() => {
  return JSON.parse(localStorage.getItem('data') || '[]');
});
```

---

## Errores comunes

> [!WARNING]
> **Mutar el estado directamente:** Nunca hagas `state.push(item)` o `state.name = 'nuevo'`. React no detectará el cambio y el componente no se re-renderizará.
>
> **Arrays:** Usa `[...arr, newItem]` o `arr.filter(...)` en lugar de `.push()` o `.splice()`.
>
> **Objetos:** Usa spread `{ ...obj, key: value }` en lugar de asignar propiedades directamente.
>
> **Demasiado estado local:** Si el estado se comparte entre muchos componentes, considera Context o un estado global. useState es para estado realmente local al componente.

---

## ¿Cuándo preferir useReducer?

Usa `useReducer` en lugar de `useState` cuando:
- El estado tiene múltiples sub-valores relacionados
- El siguiente estado depende del anterior de forma compleja
- Tienes múltiples acciones que modifican el estado de formas diferentes
- Quieres lógica de actualización testeable de forma aislada

```tsx
// useState se complica cuando hay muchas actualizaciones relacionadas
// useReducer centraliza la lógica de transformación del estado
```

Ver [[useEffect]] para la contraparte reactiva, y [[React Query]] para estado de servidor.

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación inicial |
