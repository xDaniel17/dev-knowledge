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
migratedFrom: 01 Stack/React-Hooks/Hooks-useContext.md
---

El **hook `useContext`** de React permite acceder al contexto en los componentes funcionales, proporcionando una forma eficiente de compartir datos globales como temas, preferencias de idioma o datos de usuario sin necesidad de pasar props manualmente a través de cada nivel del componente.
### ¿Cuándo usarlo?
Se debe usar `useContext` cuando:
- Deseas evitar el "prop drilling", que ocurre al pasar props innecesarias a través de múltiples niveles de componentes.
- Necesitas compartir datos globales como la autenticación del usuario, temas, o configuraciones de la aplicación en varios componentes.
- Quieres mantener el código más limpio y fácil de mantener en aplicaciones con componentes profundamente anidados.
### Sintaxis Básica
1. **Crear un Contexto**:
ECHO is off.
    ```jsx
    import React, { createContext, useContext } from 'react';  
    const MyContext = createContext(defaultValue);
    ```
ECHO is off.
2. **Proveer un Contexto**:
ECHO is off.
    ```jsx
	<MyContext.Provider value={/* some value */}>   
	{
		/* children components */
	}
	 </MyContext.Provider>
    ```
ECHO is off.
3. **Consumir un Contexto**:
```jsx
	const value = useContext(MyContext);
```
### Ejemplo
```jsx
import React, { createContext, useContext } from 'react';
// Crear el contexto
const UserContext = createContext();
// Componente que usa el contexto
const UserProfile = () => {
  const user = useContext(UserContext);  // Acceder al valor del contexto
  return (
    <div>
      <h1>Bienvenido, {user.name}</h1>
    </div>
  );
};
// Componente principal que provee el contexto
const App = () => {
  const user = { name: 'Daniel', age: 30 };
  return (
    <UserContext.Provider value={user}>
      <UserProfile />
    </UserContext.Provider>
  );
};
export default App;
```
### Buenas Prácticas
- **Mantener el Contexto Específico**: Evita crear un solo contexto para toda la aplicación. En lugar de ello, divide los contextos en partes específicas (ej. Contexto de usuario, contexto de tema).
- **Evitar Cambios Frecuentes en el Valor del Contexto**: Si el valor del contexto cambia frecuentemente, puede provocar renderizados innecesarios. Considera optimizaciones como `React.memo` o divide el contexto en partes más pequeñas.
- **Proveer Valores Definidos**: Siempre proporciona valores claros y definidos para evitar errores relacionados con `undefined`.
- **Uso Moderado**: No abuses de `useContext` para todos los datos. Úsalo para datos verdaderamente globales y para otros datos considera usar `props` o un estado local.

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
