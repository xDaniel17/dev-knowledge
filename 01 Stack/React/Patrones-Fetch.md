---
title: Buenas Prácticas
date: 2026-03-29
lastUpdated: 2026-03-29
tags: [tecnologias, desarrollo]
category: 01 Stack
author: Daniel
version: 1.0.0
status: active
related: []
migrated: true
migratedFrom: 01 Stack/React-Patrones/Fetch.md
---

Un **fetch de datos** es una operación en la que una aplicación obtiene información de una fuente externa, como una API o un servidor, generalmente a través de una solicitud HTTP. En el contexto de React o cualquier otra aplicación web, esta técnica es crucial para mostrar datos dinámicos, como publicaciones de un blog, productos en una tienda en línea o usuarios en un sistema.
## Fetch de datos en React
En React, un fetch de datos generalmente se realiza dentro de un **Hook como `useEffect`**, para asegurarse de que la solicitud se haga en el momento adecuado (por ejemplo, cuando se monta el componente).
## Sintaxis básica
``` jsx
fetch(url, opciones)
  .then((response) => {
    if (response.ok) {
      throw new Error(`HTTP error ${response.status}`);
    }
    return response.json(); // Convierte la respuesta a JSON
  })
  .then((data) => {
    console.log(data); // Maneja los datos obtenidos
  })
  .catch((error) => {
    console.error('Error en la solicitud:', error); // Maneja errores
  });
```
`Async:`
```tsx
async function obtenerPosts() {
  try {
    const response = await fetch('https://jsonplaceholder.typicode.com/posts');
    if (response.ok) {
      throw new Error(`Error HTTP: ${response.status}`);
    }
    const data = await response.json();
    console.log(data);
  } catch (error) {
    console.error('Error en la solicitud:', error);
  }
}
obtenerPosts();
```
## Buena practica 
- Aislar del componente
- Endpoint en variable
## Ejemplo de fetch de datos en un componente funcional
```tsx
import React, { useState, useEffect } from 'react';
function ListaDeUsuarios() {
  const [usuarios, setUsuarios] = useState([]);
  const [cargando, setCargando] = useState(true);
  const url_endpoint = 'https://jsonplaceholder.typicode.com/users'
  useEffect(() => {
    fetch(url_endpoint)
      .then((response) => response.json())
      .then((data) => {
        setUsuarios(data); // Almacena los datos en el estado
        setCargando(false); // Cambia el estado de carga
      })
      .catch((error) => console.error('Error al obtener los datos:', error));
  }, []); // El array vacío asegura que el efecto se ejecute solo una vez
  if (cargando) {
    return <p>Cargando usuarios...</p>;
  }
  return (
    <ul>
      {usuarios.map((usuario) => (
        <li key={usuario.id}>{usuario.name}</li>
      ))}
    </ul>
  );
}
export default ListaDeUsuarios;
```



---

## 📋 Historial de Versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel Herrera | Versión inicial |


## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-29 | Daniel | Migración desde personal-knowledge |
