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
migratedFrom: 01 Stack/React-Hooks/Hooks-useState.md
---

**`useState`** es un Hook en React que permite manejar el estado en componentes funcionales. El estado se utiliza para almacenar y actualizar información que cambia con el tiempo, como datos de entrada de formularios, contadores o respuestas de una API.
### ¿Cuándo usarlo?
- **Estado Local Simple**: Para manejar el estado local que solo afecta a un componente específico.
- **Entrada de Usuario**: Almacenar datos temporales como el valor de un campo de entrada o checkbox.
- **Toggles**: Gestionar cambios de estado simples como mostrar/ocultar contenido.
### Sintaxis básica
```tsx
const [estado, setEstado] = useState(valorInicial);
```
### Parámetros de `useState`
- **`valorInicial` (entrada):** Es el valor inicial del estado, que puede ser cualquier tipo (número, cadena, objeto, etc.). También puede ser una función para calcular el valor inicial solo al montar el componente.
### Valores devueltos:
1. **`estado`:** El valor actual del estado, que es inmutable.
2. **`setEstado`:** Una función que actualiza el estado. Puede recibir:
    - Un nuevo valor directamente: `setEstado(nuevoValor)`.
    - Una función basada en el estado previo: `setEstado(prevEstado => nuevoEstado)`.
### Ejemplo básico
Un contador que aumenta cuando se hace clic en un botón:
```tsx
import React, { useState } from 'react';
const Formulario: React.FC = () => {
  // Estado tipado como una cadena para manejar el texto ingresado
  const [nombre, setNombre] = useState<string>('');
  return (
    <div>
      <h1>Formulario</h1>
      <input
        type="text"
        value={nombre}
        onChange={(e: React.ChangeEvent<HTMLInputElement>) => setNombre(e.target.value)}
        placeholder="Escribe tu nombre"
      />
      <p>Hola, {nombre}</p>
    </div>
  );
};
export default Formulario;
```
### Buenas Prácticas
- **Estado Simple**: Usa `useState` para manejar estados simples. Para estados más complejos o múltiples valores relacionados, considera `useReducer`.
- **Actualizar Basado en el Estado Anterior**: Usa la función de actualización con un callback para asegurarte de tener siempre el estado más reciente:
ECHO is off.
```jsx
    setCount(prevCount => prevCount + 1);`
    ```
ECHO is off.
- **Evitar Actualizaciones Innecesarias**: Asegúrate de no llamar `setState` innecesariamente para evitar renderizados redundantes.
- **Dividir Estado**: Si un componente tiene múltiples piezas de estado no relacionadas, es mejor dividirlas en múltiples llamadas a `useState` en lugar de agruparlas en un solo objeto.
`useState` es una herramienta esencial en el arsenal de cualquier desarrollador de React, proporcionando una manera simple y efectiva de manejar el estado en componentes funcionales.

---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
