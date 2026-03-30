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
migratedFrom: 01 Stack/React-Hooks/Hooks-useEffect.md
---

**`useEffect`** es un Hook en React que permite manejar efectos secundarios en componentes funcionales. Se utiliza para ejecutar lógica que debe ocurrir como respuesta a cambios en el estado, las props o al ciclo de vida del componente (como cuando se monta, actualiza o desmonta).
## Sintaxis basica
``` tsx
useEffect(() => {
  // Lógica del efecto
  return () => {
    // Limpieza opcional
  };
}, [dependencias]);
```
### Parámetros de `useEffect`
1. **La función de efecto (`callback`):**
ECHO is off.
    - Contiene la lógica que React debe ejecutar.
    - Puede opcionalmente devolver una función de limpieza.
2. **El array de dependencias (`[]`):**
ECHO is off.
    - Determina cuándo se ejecutará el efecto.
    - Si está vacío (`[]`), el efecto se ejecuta una sola vez al montar el componente.
    - Si contiene dependencias, el efecto se ejecutará cada vez que cambien.
### Buenas practicas
- Cada useEffect debe tener una unica responsabilidad
- No olvidar colocar el [ ]  para que la función solo se ejecute una vez al renderizar el componente
- Tomar en cuenta que la función de retorno sirve para limpiar o inicializar el efecto
### Ejemplo Básico
```tsx
import React, { useState, useEffect } from 'react';
// Definir el tipo para un usuario
interface Usuario {
  id: number;
  name: string;
  email: string;
}
const ListaUsuarios: React.FC = () => {
  // Estado para la lista de usuarios
  const [usuarios, setUsuarios] = useState<Usuario[]>([]);
  // Estado para manejar la carga
  const [cargando, setCargando] = useState<boolean>(true);
  // Estado para manejar errores
  const [error, setError] = useState<string | null>(null);
  // `useEffect` para obtener datos de la API cuando el componente se monta
  useEffect(() => {
    const obtenerUsuarios = async () => {
      try {
        const respuesta = await fetch('https://jsonplaceholder.typicode.com/users');
        if (respuesta.ok) {
          throw new Error('Error al obtener los usuarios');
        }
        const datos: Usuario[] = await respuesta.json();
        setUsuarios(datos); // Guardar los usuarios en el estado
        setError(null); // Limpiar errores previos
      } catch (err: unknown) {
        setError((err as Error).message); // Manejar errores
      } finally {
        setCargando(false); // Cambiar el estado de carga
      }
    };
    obtenerUsuarios();
  }, []); // Array vacío: se ejecuta solo una vez al montar el componente
  // Mostrar un mensaje mientras se cargan los datos
  if (cargando) return <p>Cargando usuarios...</p>;
  // Mostrar un mensaje si ocurrió un error
  if (error) return <p>Error: {error}</p>;
  // Mostrar la lista de usuarios si todo está bien
  return (
    <div>
      <h1>Lista de Usuarios</h1>
      <ul>
        {usuarios.map((usuario) => (
          <li key={usuario.id}>
            {usuario.name} ({usuario.email})
          </li>
        ))}
      </ul>
    </div>
  );
};
export default ListaUsuarios;
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
