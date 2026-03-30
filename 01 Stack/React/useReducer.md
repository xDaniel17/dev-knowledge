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
migratedFrom: 01 Stack/React-Hooks/Hooks-useReducer.md
---

El **hook `useReducer`** de React es una alternativa a `useState` que se utiliza para manejar estados complejos y lógicos en componentes funcionales, especialmente cuando las actualizaciones de estado implican múltiples subvalores o lógica condicional.
## Fuentes
- [Tutorial YouTube](Tienda y Carrito con React + Estado Global con useContext + Manejo de estado con useReducer](https://www.youtube.com/watch?v=B9tDYAZZxcE)
- [Código Fuente Ejemplo Carrito de compras](https://github.com/midudev/aprendiendo-react/tree/master/projects/06-shopping-cart)
- [Otro Ejemplo de uso (TypeScipt)](https://youtu.be/kZhabulNCUc?list=PLUofhDIg_38q4D0xNWp7FEHOTcZhjWJ29&t=624)
### ¿Cuándo usarlo?
Se recomienda usar `useReducer` en los siguientes casos:
- Cuando el estado es complejo y tiene múltiples subvalores que dependen unos de otros.
- Si las actualizaciones del estado dependen de la lógica condicional o múltiple.
- Cuando se busca una mejor organización del estado y lógica de actualización en un componente.
### Sintaxis Básica
```jsx
const [state, dispatch] = useReducer(reducer, initialState);`
```
- **`reducer`**: una función que recibe el estado actual y una acción, y devuelve un nuevo estado.
- **`initialState`**: el estado inicial.
### Ejemplo
```jsx
import React, { useReducer } from 'react';
// Definir el reductor
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    default:
      throw new Error();
  }
}
// Componente que usa useReducer
function Counter() {
  const initialState = { count: 0 };
  const [state, dispatch] = useReducer(reducer, initialState);
  return (
    <div>
      <p>Contador: {state.count}</p>
      <button onClick={() => dispatch({ type: 'increment' })}>Incrementar</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>Decrementar</button>
    </div>
  );
}
export default Counter;
```
### Otro ejemplo utilizando TypeScript
[Código Fuente](https://github.com/midudev/aprendiendo-react/tree/master/projects/09-google-translate-clone)
```ts
import { useReducer } from 'react'
import { AUTO_LANGUAGE } from '../constants'
import { type FromLanguage, type Language, type Action, type State } from '../types'
// 1. Create a initialState
const initialState: State = {
  fromLanguage: 'auto',
  toLanguage: 'en',
  fromText: '',
  result: '',
  loading: false
}
// 2. Create a reducer
function reducer (state: State, action: Action) {
  const { type } = action
  if (type === 'INTERCHANGE_LANGUAGES') {
    // lógica del estado dentro del reducer
    // porque lo evitamos en los componentes
    if (state.fromLanguage === AUTO_LANGUAGE) return state
    const loading = state.fromText == ''
    return {
      ...state,
      loading,
      result: '',
      fromLanguage: state.toLanguage,
      toLanguage: state.fromLanguage
    }
  }
  if (type === 'SET_FROM_LANGUAGE') {
    if (state.fromLanguage === action.payload) return state
    const loading = state.fromText == ''
    return {
      ...state,
      fromLanguage: action.payload,
      result: '',
      loading
    }
  }
  if (type === 'SET_TO_LANGUAGE') {
    if (state.toLanguage === action.payload) return state
    const loading = state.fromText == ''
    return {
      ...state,
      toLanguage: action.payload,
      result: '',
      loading
    }
  }
  if (type === 'SET_FROM_TEXT') {
    const loading = action.payload == ''
    return {
      ...state,
      loading,
      fromText: action.payload,
      result: ''
    }
  }
  if (type === 'SET_RESULT') {
    return {
      ...state,
      loading: false,
      result: action.payload
    }
  }
  return state
}
export function useStore () {
  // 3. usar el hook useReducer
  const [{
    fromLanguage,
    toLanguage,
    fromText,
    result,
    loading
  }, dispatch] = useReducer(reducer, initialState)
  const interchangeLanguages = () => {
    dispatch({ type: 'INTERCHANGE_LANGUAGES' })
  }
  const setFromLanguage = (payload: FromLanguage) => {
    dispatch({ type: 'SET_FROM_LANGUAGE', payload })
  }
  const setToLanguage = (payload: Language) => {
    dispatch({ type: 'SET_TO_LANGUAGE', payload })
  }
  const setFromText = (payload: string) => {
    dispatch({ type: 'SET_FROM_TEXT', payload })
  }
  const setResult = (payload: string) => {
    dispatch({ type: 'SET_RESULT', payload })
  }
  return {
    fromLanguage,
    toLanguage,
    fromText,
    result,
    loading,
    interchangeLanguages,
    setFromLanguage,
    setToLanguage,
    setFromText,
    setResult
  }
}
```
### Buenas Prácticas
- **Desglosar la Lógica**: Mantén el reductor limpio y enfocado. Si la lógica se vuelve demasiado compleja, considera dividirla en funciones más pequeñas.
- **Usar Constantes para Tipos de Acción**: Define constantes para los tipos de acción para evitar errores tipográficos y facilitar el mantenimiento del código.
- **Evitar Side Effects en el Reductor**: El reductor debe ser una función pura. Los efectos secundarios (como llamadas a APIs) deben manejarse fuera del reductor, por ejemplo, en un `useEffect`.
- **Mantener el Estado Inmutable**: Asegúrate de no mutar el estado directamente en el reductor. Siempre devuelve un nuevo objeto de estado.
`useReducer` es particularmente útil en aplicaciones donde el estado y las actualizaciones son complejos, ofreciendo una estructura más clara y mantenible que `useState` para estos casos.

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
