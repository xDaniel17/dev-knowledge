---
title: Comparativa Rápida de Hooks
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - react
  - hooks
  - comparativa
  - referencia rápida
category: "01 Stack"
subcategory: "React"
author: Daniel
version: 1.0.0
status: active
related: []
---

# 🎯 Comparativa Rápida de React Hooks

Cheat sheet visual para elegir rápidamente el hook correcto.

---

## 📌 Referencia de Uso Común (Copy-Paste Ready)

### Estado Simple
```tsx
// ✅ Para estados simples
const [count, setCount] = useState(0);

// ❌ NO hacer esto:
const [data, setData] = useState({ count: 0, name: '', email: '' }); // Considerar useReducer
```

### Fetch de Datos
```tsx
// ✅ Patrón correcto
useEffect(() => {
  let isMounted = true;
  
  fetchData().then(data => {
    if (isMounted) setData(data);
  }).catch(err => {
    if (isMounted) setError(err);
  });
  
  return () => { isMounted = false; };
}, []);
```

### Memoizar Callback
```tsx
// ✅ Para pasar a componentes memoizados
const handleClick = useCallback(() => {
  doSomething(id);
}, [id]);

// ❌ NO hacer esto:
const handleClick = useCallback(() => {
  doSomething(id); // 'id' no está en dependencias
}, []);
```

### Estado Complejo
```tsx
// ✅ Mejor que múltiples useState
const [state, dispatch] = useReducer(reducer, {
  count: 0,
  name: '',
  email: ''
});

// ❌ NO hacer esto:
const [count, setCount] = useState(0);
const [name, setName] = useState('');
const [email, setEmail] = useState('');
```

---

## ⚡ Matriz de Decisión Rápida

```
┌─ ¿Cuál hook necesito?
│
├─ ¿Estado?
│  ├─ Simple (uno o dos valores) → useState
│  ├─ Complejo (múltiples valores) → useReducer
│  └─ Estado remoto (server) → SWR / React Query
│
├─ ¿Compartir datos?
│  ├─ Entre componentes próximos → Lifting state up
│  ├─ Datos globales → useContext
│  └─ Estado externo (Redux) → useSyncExternalStore
│
├─ ¿Performance?
│  ├─ Evitar cálculos → useMemo
│  ├─ Pasar callbacks → useCallback
│  └─ Actualización lenta → useTransition / useDeferredValue
│
├─ ¿Acceso al DOM?
│  ├─ Ref simple → useRef
│  ├─ Métodos imperatives → useImperativeHandle
│  └─ Sincrónico con DOM → useLayoutEffect
│
├─ ¿Efectos secundarios?
│  ├─ Asincrónico (FETCH, events) → useEffect
│  └─ Sincrónico (medidas DOM) → useLayoutEffect
│
├─ ¿IDs únicos?
│  └─ Para accesibilidad → useId
│
└─ ¿Debugging?
   └─ En DevTools → useDebugValue
```

---

## 📊 Tabla Comparativa Detallada

### Hooks de Estado

| Hook | Entrada | Salida | Cuándo | Complejidad |
|------|---------|--------|--------|------------|
| `useState` | inicial | `[state, setState]` | Datos simples | 🟢 |
| `useReducer` | reducer+inicial | `[state, dispatch]` | Lógica compleja | 🟡 |
| `useSyncExternalStore` | suscriptor+snap | `state` | Estado externo | 🔴 |

### Hooks de Performance

| Hook | Qué memoriza | Dependencias | Overhead | Cuándo |
|------|-------------|--------------|----------|--------|
| `useMemo` | Valor/objeto | Sí | Medio | Cálculos costosos |
| `useCallback` | Función | Sí | Bajo | Callbacks en Memoized |
| `useTransition` | - | No | Alto | Actualizaciones lentas |
| `useDeferredValue` | Valor | No | Medio | Debounce implícito |

### Hooks de Efectos

| Hook | Timing | Bloquea Paint | Usa Cleanup | Versión |
|------|--------|---------------|-----------|---------
| `useEffect` | Asincrónico | No | Sí | 16.8 |
| `useLayoutEffect` | Sincrónico | Sí | Sí | 16.8 |
| `useInsertionEffect` | Pre-layout | Sí | Sí | 18.0 |

### Hooks de Contexto & Refs

| Hook | Lee | Escribe | Reactivo | Caso de Uso |
|------|-----|---------|----------|------------|
| `useContext` | Contexto | No | Sí | Datos compartidos |
| `useRef` | ref.current | Sí | No | DOM, valores mutables |
| `useId` | String ID | No | No | Accesibilidad |

---

## 🎭 Patrones Anti-Patrón

### ❌ Anti-patrón 1: Hooks en condicionales
```tsx
// ❌ MALO
if (condition) {
  const [state, setState] = useState(0);
}

// ✅ BIEN
const [state, setState] = useState(0);
if (condition) {
  // usar state aquí
}
```

### ❌ Anti-patrón 2: Dependencias incompletas
```tsx
// ❌ MALO
useEffect(() => {
  console.log(userId); // userId no está en dependencias
  fetchUser(userId);
}, []); // Loop infinito o stale closure

// ✅ BIEN
useEffect(() => {
  fetchUser(userId);
}, [userId]); // Re-run cuando userId cambia
```

### ❌ Anti-patrón 3: Mutation directa
```tsx
// ❌ MALO
state.name = 'nuevo';
setState(state);

// ✅ BIEN
setState({ ...state, name: 'nuevo' });
// O
setState(prev => ({ ...prev, name: 'nuevo' }));
```

### ❌ Anti-patrón 4: useCallback sin razón
```tsx
// ❌ MALO (overhead sin beneficio)
const handleClick = useCallback(() => {
  setCount(count + 1);
}, [count]); // Se recrea cada vez anyway

// ✅ BIEN (solo si pasas a memoized component)
const handleClick = useCallback(() => {
  setCount(c => c + 1);
}, []); // Nunca cambia porque usa updater
```

### ❌ Anti-patrón 5: useEffect para transformar props
```tsx
// ❌ MALO
useEffect(() => {
  setProcessed(processData(data));
}, [data]);

// ✅ BIEN
const processed = useMemo(() => processData(data), [data]);
// O incluso mejor, no tener state
const processed = processData(data);
```

---

## 💡 Heurísticas Útiles

### Regla 1: Una Responsabilidad por Hook
Cada hook debe hacer una cosa clara:

```tsx
// ❌ MALO
useEffect(() => {
  fetchUser(id);
  subscribeToUpdates(id);
  loadSettings();
}, [id]);

// ✅ BIEN
useEffect(() => { fetchUser(id); }, [id]);
useEffect(() => { subscribeToUpdates(id); }, [id]);
useEffect(() => { loadSettings(); }, []);
```

### Regla 2: Hooks en el Mismo Orden
React depende del orden de los hooks:

```tsx
// ❌ MALO
if (showDetail) {
  const [detail, setDetail] = useState(null);
}
const [name, setName] = useState('');

// ✅ BIEN
const [name, setName] = useState('');
const [detail, setDetail] = useState(null);
```

### Regla 3: Medir Antes de Optimizar
```tsx
// ❌ NO hagas esto sin medir
const expensive = useMemo(() => veryComplexCalc(data), [data]);

// ✅ Primero verifica si es cuello de botella
// Usa DevTools Performance tab
// Solo si hay problema real, agrega memoization
```

---

## 🚀 Migración Entre Hooks

### De Props Drilling a useContext
```tsx
// Antes: múltiples levels de props
<Parent user={user}>
  <Child user={user}>
    <GrandChild user={user} />
  </Child>
</Parent>

// Después: useContext
const UserContext = createContext();
<UserContext.Provider value={user}>
  <Child />
</UserContext.Provider>

function GrandChild() {
  const user = useContext(UserContext);
}
```

### De Class Component a Functional
```tsx
// Antes: componentDidMount + componentWillUnmount
componentDidMount() { subscribe(); }
componentWillUnmount() { unsubscribe(); }

// Después: useEffect con cleanup
useEffect(() => {
  subscribe();
  return () => unsubscribe();
}, []);
```

### De Multiple useState a useReducer
```tsx
// Antes: múltiples estados
const [name, setName] = useState('');
const [email, setEmail] = useState('');
const [error, setError] = useState('');

// Después: reducer centralizado
const [state, dispatch] = useReducer(reducer, {
  name: '',
  email: '',
  error: ''
});
```

---

## 📚 Referencias Rápidas

### Para cada hook, pregúntate:
1. **¿Es necesario?** - ¿Hay alternativa más simple?
2. **¿Están las dependencias correctas?** - ESLint rule?
3. **¿Se ejecuta cuando debería?** - DevTools hooks profiler
4. **¿Hay memory leak?** - ¿Hay cleanup?
5. **¿Es performance issue real?** - ¿O es prematura optimización?

---

## 🔗 Enlaces Relacionados

- [[Hooks-Catalogo.md|Catálogo Completo de Hooks]]
- [[_README.md|Guía Principal de React]]
- [[Patrones-Componentes.md|Patrones de Componentes]]
- [[Patrones-Buenas practicas.md|Mejores Prácticas]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 1.0.0 | 2026-03-30 | Daniel | Creación de comparativa rápida |
