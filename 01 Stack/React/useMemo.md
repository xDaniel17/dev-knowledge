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
migratedFrom: 01 Stack/React-Hooks/Hooks-useMemo.md
---

En React, **`useMemo`** es un hook que sirve para memorizar el resultado de una función costosa o compleja y evitar que se vuelva a calcular innecesariamente en cada render. Esto mejora el rendimiento al reducir cálculos redundantes.
## Sintaxis
```jsx
const memoizedValue = useMemo(() => computeFunction, [dependencies]);
```
- **`computeFunction`**: Es la función cuyo resultado será memorizado.
- **`dependencies`**: Es un array de dependencias que determina cuándo volver a calcular el valor. Si alguna dependencia cambia, la función se vuelve a ejecutar.
### ¿Cuándo usar `useMemo`?
- Cuando una operación es computacionalmente costosa (como cálculos matemáticos complejos o procesamiento de grandes listas) **Problemas de rendimiento**.
- Para evitar cálculos innecesarios que dependen de valores que cambian con poca frecuencia.
### Ejemplo básico: Calcular un valor costoso
Supongamos que queremos calcular un número grande solo si un valor cambia.
```jsx
import React, { useState, useMemo } from "react";
function ExpensiveCalculationComponent() {
  const [count, setCount] = useState(0);
  const [otherState, setOtherState] = useState(0);
  const expensiveCalculation = (num) => {
    console.log("Calculando...");
    for (let i = 0; i < 1e6; i++) {
      num += 1;
    }
    return num;
  };
  // Usamos useMemo para evitar recalcular si 'count' no cambia
  const memoizedValue = useMemo(() => expensiveCalculation(count), [count]);
  return (
    <div>
      <h2>Contador: {count}</h2>
      <button onClick={() => setCount(count + 1)}>Incrementar Contador</button>
      <h2>Otro Estado: {otherState}</h2>
      <button onClick={() => setOtherState(otherState + 1)}>Cambiar Otro Estado</button>
      <h3>Cálculo Costoso: {memoizedValue}</h3>
    </div>
  );
}
export default ExpensiveCalculationComponent;
```
#### Explicación:
- **Sin `useMemo`:** Cada vez que actualizas `otherState`, se vuelve a ejecutar la función `expensiveCalculation`, incluso si `count` no cambia.
- **Con `useMemo`:** La función `expensiveCalculation` solo se ejecuta si el valor de `count` cambia.
### Diferencia entre `useMemo` y `useCallback`
|Característica|`useMemo`|`useCallback`|
|---|---|---|
|¿Qué memoriza?|El **resultado** de una función.|La **referencia** de una función.|
|¿Cuándo usarlo?|Para evitar cálculos innecesarios.|Para evitar recreación de funciones.|
|Valor devuelto|El valor calculado.|La función memorizada.|
### Buenas prácticas:
1. **No abuses de `useMemo`:** Memorizar operaciones simples o rápidas puede ser contraproducente.
2. **Úsalo solo para cálculos costosos o listas grandes.**
3. **Asegúrate de incluir todas las dependencias necesarias en el array.**
`useMemo` es una herramienta poderosa cuando se usa de forma adecuada, pero no es necesario para todas las aplicaciones.

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
