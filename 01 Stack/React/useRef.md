---
title: Guía de Hooks
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [tecnologias, desarrollo]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/React-Hooks/Hooks-useRef.md
---

El hook **`useRef`** en React se utiliza para crear una referencia mutable que persiste a lo largo del ciclo de vida del componente sin provocar un nuevo render cuando su valor cambia. Es muy útil para interactuar con elementos DOM o para almacenar valores que no necesitan causar un render al modificarse.
##Sintaxis
```jsx
const ref = useRef(initialValue);
```
- **`initialValue`**: Es el valor inicial de la referencia.
- **`ref.current`**: Es el valor actual de la referencia y puede ser leído o actualizado directamente.
### ¿Cuándo usar `useRef`?
1. **Acceso directo al DOM:** Para interactuar con elementos DOM sin necesidad de usar `document.querySelector`.
2. **Persistencia de valores:** Almacenar valores que cambian pero no necesitan desencadenar un re-render (como un contador o estado temporal).
3. **Referencias a componentes:** Mantener una referencia a un componente o función.
### Ejemplo 1: Acceso directo al DOM
En este ejemplo, usaremos `useRef` para enfocarnos en un campo de entrada.
```jsx
import React, { useRef } from "react";
function InputFocus() {
  const inputRef = useRef(null);
  const handleFocus = () => {
    inputRef.current.focus(); // Enfoca el input
  };
  return (
    <div>
      <input ref={inputRef} type="text" placeholder="Escribe algo..." />
      <button onClick={handleFocus}>Enfocar Input</button>
    </div>
  );
}
export default InputFocus;
```
#### Explicación:
- `useRef` crea una referencia al elemento `<input>`.
- Con `inputRef.current.focus()`, accedemos al DOM directamente para enfocar el campo de entrada.
### Ejemplo 2: Persistencia de valores entre renders
En este ejemplo, usamos `useRef` para almacenar un contador que no afecta el renderizado.
```jsx
import React, { useState, useRef } from "react";
function Counter() {
  const [count, setCount] = useState(0);
  const renderCount = useRef(0);
  renderCount.current += 1;
  return (
    <div>
      <h2>Contador: {count}</h2>
      <button onClick={() => setCount(count + 1)}>Incrementar</button>
      <p>Renders: {renderCount.current}</p>
    </div>
  );
}
export default Counter;
```
#### Explicación:
- `renderCount` se actualiza en cada render pero no causa un nuevo render porque `useRef` no está vinculado al ciclo de renderización.
- Esto es útil para rastrear información o estadísticas internas.
### Buenas prácticas:
1. **No uses `useRef` como sustituto de `useState`:** Si un cambio en el valor necesita reflejarse en la UI, usa `useState` en lugar de `useRef`.
2. **Evita abusar de `useRef` para manipular el DOM:** Siempre que sea posible, utiliza React para manejar la manipulación del DOM.
3. **Cuidado con el acceso directo al DOM:** Esto puede llevar a inconsistencias si React y el DOM real no están sincronizados.

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
