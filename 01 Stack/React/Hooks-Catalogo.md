---
title: Catálogo Completo de React Hooks
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - react
  - hooks
  - referencia
  - catálogo
category: "01 Stack"
subcategory: "React"
author: Daniel
version: 2.0.0
status: active
related: []
---

# 🪝 Catálogo Completo de React Hooks

Referencia visual y rápida de todos los React Hooks disponibles hasta React 19, organizados por categoría y caso de uso.

---

## 📋 Tabla Rápida de Referencia

| Categoría | Hook | Descripción | Versión | Complejidad |
|-----------|------|-------------|---------|------------|
| **Estado** | `useState` | Agregar estado local | 16.8 | 🟢 |
| **Estado** | `useReducer` | Estado complejo | 16.8 | 🟡 |
| **Efectos** | `useEffect` | Efectos secundarios | 16.8 | 🟢 |
| **Efectos** | `useLayoutEffect` | Efectos síncronos | 16.8 | 🔴 |
| **Efectos** | `useInsertionEffect` | Inyectar CSS dinámico | 18.0 | 🔴 |
| **Contexto** | `useContext` | Acceso a contexto | 16.8 | 🟢 |
| **Performance** | `useMemo` | Memoizar valores | 16.8 | 🟡 |
| **Performance** | `useCallback` | Memoizar funciones | 16.8 | 🟡 |
| **Referencias** | `useRef` | Acceso al DOM directo | 16.8 | 🟢 |
| **IDs** | `useId` | Generar IDs únicos | 18.0 | 🟢 |
| **Estado** | `useState` (hook) | - | 19.0 | 🟢 |
| **Transiciones** | `useTransition` | Estado asincrónico | 18.0 | 🟡 |
| **Estado suspendido** | `useDeferredValue` | Diferir actualizaciones | 18.0 | 🟡 |
| **Estado externo** | `useSyncExternalStore` | Suscribirse a estado externo | 18.0 | 🔴 |
| **Debugging** | `useDebugValue` | Valor de debug | 16.8 | 🟡 |
| **Imperativo** | `useImperativeHandle` | Exponer manejadores imperativo | 16.8 | 🔴 |
| **Eventos** | `useEvent` | Handlers estables | 19.0 | 🟡 |
| **Callback** | `useActionState` | Form actions con estado | 19.0 | 🟡 |
| **Contexto** | `useContextSelector` | Seleccionar valor de contexto | - | 🟡 |

---

## 🟢 HOOKS BÁSICOS (Nivel Principiante)

### 1. **useState** - Estado Local
[[useState.md]] - Agregar y actualizar estado en componentes funcionales

```tsx
const [count, setCount] = useState(0);
const [name, setName] = useState('');
```

**Cuándo usarlo:**
- Estado local simple
- Entrada de usuario
- Toggles

**Casos de uso:**
- Contadores
- Formularios
- Visibilidad (modales, menús)

---

### 2. **useRef** - Referencias y Valores Mutables
[[useRef.md]] - Acceso directo al DOM y almacenamiento mutable

```tsx
const inputRef = useRef<HTMLInputElement>(null);
const timerRef = useRef<NodeJS.Timeout | null>(null);
```

**Cuándo usarlo:**
- Acceso directo al DOM
- Almacenar valores que no deben causar re-render
- Guardar timers/intervals

**Casos de uso:**
- Focus en inputs
- Reproducción de video/audio
- Integrar con librerías no-React

---

### 3. **useContext** - Compartir Datos
[[useContext.md]] - Acceder a valores de contexto

```tsx
const theme = useContext(ThemeContext);
const user = useContext(UserContext);
```

**Cuándo usarlo:**
- Compartir datos sin prop drilling
- Temas globales
- Datos de usuario autenticado

**Casos de uso:**
- Theme (light/dark)
- Idioma (i18n)
- Datos de sesión

---

### 4. **useId** - IDs Únicos
[[useId.md]] - Generar IDs estables para accesibilidad

```tsx
const id = useId();
<label htmlFor={id}>Nombre</label>
<input id={id} />
```

**Cuándo usarlo:**
- Conectar labels con inputs
- Generar IDs en listas
- Accesibilidad

**Versión:** React 18.0+

---

## 🟡 HOOKS INTERMEDIOS (Nivel Intermedio)

### 5. **useEffect** - Efectos Secundarios
[[useEffect.md]] - Ejecutar efectos después de render

```tsx
useEffect(() => {
  // Código a ejecutar
  return () => {
    // Cleanup (opcional)
  };
}, [dependencies]);
```

**Cuándo usarlo:**
- Fetch de datos
- Suscripción a eventos
- Actualizar el DOM
- Sincronización con APIs

**Casos de uso:**
- Cargar datos al montar
- Listeners de eventos
- Cleanup de recursos

---

### 6. **useMemo** - Memoizar Valores
[[useMemo.md]] - Memoizar cálculos costosos

```tsx
const memoizedValue = useMemo(() => {
  return expensiveCalculation(a, b);
}, [a, b]);
```

**Cuándo usarlo:**
- Cálculos costosos
- Evitar re-renders innecesarios
- Objetos/arrays que no cambian

**Casos de uso:**
- Filtrado de listas
- Transformación de datos
- Compilación de datos

**Regla de oro:** Medir primero si realmente hay problema

---

### 7. **useCallback** - Memoizar Funciones
[[useCallback.md]] - Memoizar referencias a funciones

```tsx
const memoizedCallback = useCallback(
  (x) => x * 2,
  [/* dependencies */]
);
```

**Cuándo usarlo:**
- Pasar callbacks a componentes memoizados
- Evitar re-renders en Child Components
- Dependencias de useEffect

**Casos de uso:**
- Event handlers
- Callbacks a componentes optimizados
- Efectos que dependen de funciones

---

### 8. **useReducer** - Estado Complejo
[[useReducer.md]] - Gestionar estado con reducers

```tsx
const [state, dispatch] = useReducer(reducer, initialState);

// En tu componente
dispatch({ type: 'INCREMENT', payload: 5 });
```

**Cuándo usarlo:**
- Lógica de estado compleja
- Múltiples sub-valores
- Siguiente estado depende del anterior

**Casos de uso:**
- Carrito de compras
- Formularios complejos
- Máquinas de estado

---

### 9. **useTransition** - Transiciones de Estado
Manejo de actualizaciones asincrónicas no-blocking

```tsx
const [isPending, startTransition] = useTransition();

startTransition(() => {
  setSearchResults(results);
});
```

**Cuándo usarlo:**
- Búsquedas en tiempo real
- Actualizaciones no críticas
- Evitar bloqueos en UI

**Versión:** React 18.0+

---

### 10. **useDeferredValue** - Diferir Valores
Diferir actualización de valor para mejor performance

```tsx
const deferredValue = useDeferredValue(value);
```

**Cuándo usarlo:**
- Listas largas
- Búsqueda y filtrado
- Grandes renderizados

**Versión:** React 18.0+

---

## 🔴 HOOKS AVANZADOS (Nivel Avanzado)

### 11. **useLayoutEffect** - Efectos Síncronos
Ejecutar efectos de forma sincrónica después de DOM mutations

```tsx
useLayoutEffect(() => {
  // Se ejecuta ANTES que el navegador pinte
}, []);
```

**Cuándo usarlo:**
- Medir tamaño/posición del DOM
- Aplicar estilos antes del paint
- **Rara vez necesario**

**Diferencia con useEffect:**
- useEffect: asincrónico, no bloquea paint
- useLayoutEffect: sincrónico, bloquea paint

---

### 12. **useInsertionEffect** - Inyectar Estilos
Inyectar reglas CSS antes de que el navegador calcule layouts

```tsx
useInsertionEffect(() => {
  const style = document.createElement('style');
  style.textContent = `...`;
  document.head.appendChild(style);
}, []);
```

**Cuándo usarlo:**
- Librerías CSS-in-JS
- Inyectar estilos dinámicamente
- Casos muy específicos

**Versión:** React 18.0+

**Nota:** Generalmente para librerías, no aplicaciones

---

### 13. **useSyncExternalStore** - Estado Externo
Suscribirse a una tienda de estado externa

```tsx
const state = useSyncExternalStore(
  subscribe,
  getSnapshot,
  getServerSnapshot
);
```

**Cuándo usarlo:**
- Integrar con Redux, Zustand, Recoil
- Librerías de estado externo
- SSR con estado compartido

**Versión:** React 18.0+

---

### 14. **useImperativeHandle** - Handle Imperativo
Personalizar valor expuesto cuando se usa `ref` en componente

```tsx
const MyInput = forwardRef((props, ref) => {
  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current?.focus(),
    clear: () => inputRef.current!.value = ''
  }));
  return <input ref={inputRef} />;
});
```

**Cuándo usarlo:**
- Exponer métodos imperatives
- Crear APIs para componentes
- Integración con código imperativo

**Advertencia:** Rompe el flujo declarativo, usar con cuidado

---

### 15. **useDebugValue** - Debugging en DevTools
Mostrar valor personalizado en React DevTools

```tsx
useDebugValue(state === 'connected' ? '🟢 Connected' : '🔴 Disconnected');
```

**Cuándo usarlo:**
- Custom hooks
- Debugging en DevTools
- Información de estado útil

---

### 16. **useEvent** - Event Handlers Estables
Crear event handlers que no cambian de referencia

```tsx
const handleClick = useEvent((x) => {
  console.log(x, data); // Acceso a data actualizada
});
```

**Cuándo usarlo:**
- Event handlers que dependen de estado
- Evitar re-renders en listeners
- Handlers estables en memoized components

**Versión:** React 19.0+ (experimental)

---

### 17. **useActionState** - Form Actions
Gestionar estado de form actions del servidor

```tsx
const [state, formAction, pending] = useActionState(
  serverAction,
  initialState
);
```

**Cuándo usarlo:**
- Server Components forms
- Form submission con estado
- Progressive enhancement

**Versión:** React 19.0+

---

### 18. **use** - Unwrap Promises
Desenvuelve promesas y valores async

```tsx
const user = use(fetchUser());
const theme = use(ThemeContext);
```

**Cuándo usarlo:**
- Async data fetching
- Simplificar async logic
- Client Components

**Versión:** React 19.0+ (experimental)

---

## 🛠️ CUSTOM HOOKS (Crear Tus Propios)

Los custom hooks son funciones de JavaScript que usan hooks de React:

```tsx
// useLocalStorage - Persistir en localStorage
function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch {
      return initialValue;
    }
  });

  const setValue = (value: T) => {
    try {
      setStoredValue(value);
      window.localStorage.setItem(key, JSON.stringify(value));
    } catch (error) {
      console.error(error);
    }
  };

  return [storedValue, setValue] as const;
}

// Uso
const [theme, setTheme] = useLocalStorage('theme', 'light');
```

### Custom Hooks Comunes

| Hook | Descripción | Uso |
|------|-------------|-----|
| `useLocalStorage` | Persistir en localStorage | Preferencias de usuario |
| `useFetch` | Fetch con error/loading | Data fetching |
| `useAsync` | Manejar promesas | Datos asincronos |
| `usePrevious` | Almacenar valor anterior | Comparaciones |
| `useMount` | Ejecutar al montar | Inicialización |
| `useUnmount` | Ejecutar al desmontar | Cleanup |
| `useUpdateEffect` | useEffect que ignora montar | Reactions |
| `useWindowSize` | Tamaño de ventana | Responsive |
| `useMediaQuery` | Consultas media CSS | Responsive |
| `useOnline` | Estado de conexión | Offline-first |
| `useDebounce` | Debounce de valores | Search, resize |
| `useThrottle` | Throttle de funciones | Scroll, resize |

---

## 📊 Matriz de Decisión

### ¿Qué hook usar?

```
¿Necesitas estado?
├─ Sí, simple → useState
├─ Sí, complejo → useReducer
└─ No

¿Necesitas efectos secundarios?
├─ Sí, sincrónico → useLayoutEffect
├─ Sí, asincrónico → useEffect
└─ No

¿Necesitas compartir datos?
├─ Sí → useContext
└─ No

¿Necesitas performance?
├─ Recalcular valores → useMemo
├─ Pasar callbacks → useCallback
└─ Diferir actualizaciones → useDeferredValue

¿Necesitas acceso directo al DOM?
├─ Sí → useRef
└─ No

¿Necesitas IDs únicos?
├─ Sí → useId
└─ No
```

---

## 🎯 Patrones Comunes

### Pattern 1: Fetch de Datos
```tsx
useEffect(() => {
  let isMounted = true;
  
  fetchData().then(data => {
    if (isMounted) setData(data);
  });
  
  return () => { isMounted = false; };
}, []);
```

### Pattern 2: Validación en Tiempo Real
```tsx
const [email, setEmail] = useState('');
const [errors, setErrors] = useState('');

useEffect(() => {
  const timer = setTimeout(() => {
    setErrors(validateEmail(email));
  }, 500);
  
  return () => clearTimeout(timer);
}, [email]);
```

### Pattern 3: Sincronización Entre Tabs
```tsx
useEffect(() => {
  const handler = () => {
    const data = localStorage.getItem('shared');
    if (data) setData(JSON.parse(data));
  };
  
  window.addEventListener('storage', handler);
  return () => window.removeEventListener('storage', handler);
}, []);
```

---

## 🚫 Errores Comunes

| Error | Problema | Solución |
|-------|----------|----------|
| **Dependencia faltante en useEffect** | Loop infinito o stale closure | Agregar todas las dependencias |
| **Hooks en condicionales** | Cambio en orden de hooks | Mover hook fuera del condicional |
| **Mutation directa de estado** | No detecta cambios | Crear nuevo objeto/array |
| **useCallback sin dependencias** | Función nunca se memoiza | Especificar dependencias |
| **useMemo para todo** | Overhead sin beneficio | Solo para cálculos costosos |

---

## 📚 Recursos

- [[_README.md|Guía Principal de React]]
- [[Patrones-Componentes.md|Patrones de Componentes]]
- [[Patrones-Fetch.md|Patrones de Fetch]]
- [[Patrones-Buenas practicas.md|Mejores Prácticas]]

---

## 📦 Librerías Útiles

Para custom hooks listos para usar:
- **react-use** - Collection de hooks
- **ahooks** - Hooks de alibaba
- **usehooks-ts** - TypeScript hooks
- **SWR** - Data fetching
- **TanStack Query** - Data fetching avanzado

---

## 🔗 Enlaces Relacionados

- [[../../Web-Conceptos/_README.md|Web Conceptos]]
- [[../../Next.js/App Router.md|Next.js]]
- [[../../00 Fundamentos/README|Fundamentos]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Catálogo completo de hooks (18 hooks + custom) |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
