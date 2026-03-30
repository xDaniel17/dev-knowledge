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
migratedFrom: 01 Stack/React-Hooks/Hooks-useCallback.md
---

El hook **`useCallback`** en React se utiliza para memorizar una función y evitar que se recree en cada render. Es útil cuando pasas funciones como props a componentes hijos o cuando las funciones dependen de valores que cambian poco frecuentemente.
### Sintaxis
```jsx
const memoizedCallback = useCallback(() => {
  // Lógica de la función
}, [dependencies]);
```
**`dependencies`**: Es un array de dependencias que determina cuándo debe volverse a crear la función. Si alguna dependencia cambia, la función será recreada.
### ¿Cuándo usar `useCallback`?
- Cuando pasas funciones como props a componentes hijos (especialmente si los hijos usan `React.memo`).
- Para evitar la recreación de funciones en cada render y mejorar el rendimiento.
### Ejemplo básico: Pasar funciones memorizadas
Supongamos que tienes un componente hijo que recibe una función como prop:
```jsx
import React, { useState, useCallback } from "react";
function Child({ onClick }) {
  console.log("Renderizando Child...");
  return (
    <button onClick={onClick}>Hacer algo</button>
  );
}
const MemoizedChild = React.memo(Child);
function Parent() {
  const [count, setCount] = useState(0);
  const handleClick = useCallback(() => {
    console.log("¡Click en el botón del hijo");
  }, []);
  return (
    <div>
      <h2>Contador: {count}</h2>
      <button onClick={() => setCount(count + 1)}>Incrementar Contador</button>
      <MemoizedChild onClick={handleClick} />
    </div>
  );
}
export default Parent;
```
#### Explicación:
- **Sin `useCallback`:** En cada render del componente padre, la función `handleClick` se recrea y hace que el componente hijo también se renderice.
- **Con `useCallback`:** La función `handleClick` permanece memorizada mientras sus dependencias no cambien, lo que evita renders innecesarios en el componente hijo.
-
### Diferencia entre `useMemo` y `useCallback`
|Característica|`useMemo`|`useCallback`|
|---|---|---|
|¿Qué memoriza?|El **resultado** de una función.|La **referencia** de una función.|
|¿Cuándo usarlo?|Para evitar cálculos innecesarios.|Para evitar recreación de funciones.|
|Valor devuelto|El valor calculado.|La función memorizada.|
### Buenas prácticas:
1. **Úsalo solo cuando sea necesario optimizar el rendimiento.** Si no tienes problemas de rendimiento, no es necesario usar `useCallback`.
2. **Incluye todas las dependencias necesarias** en el array para evitar comportamientos inesperados.
3. **No memorices funciones complejas innecesariamente.** Esto puede agregar complejidad sin beneficio.
`useCallback` es una herramienta valiosa para optimizar componentes, especialmente cuando trabajas con componentes hijos memorizados o eventos frecuentes.

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
