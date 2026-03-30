---
title: Angular - Framework Empresarial
date: 2026-03-30
lastUpdated: 2026-03-30
tags:
  - angular
  - typescript
  - framework
  - rxjs
  - frontend
category: "01 Stack"
subcategory: "Angular"
author: Daniel
version: 2.0.0
status: active
related: []
---

# Angular

Framework TypeScript-first para construir aplicaciones web escalables. Opiniones fuertes, arquitectura robusta.

---

## 📚 Documentación Completa

### Conceptos Fundamentales
[[Angular.md]] - Introducción a Angular
- Qué es Angular
- TypeScript
- Módulos y Componentes
- Inyección de dependencias
- Decorators

### Setup y Creación de Proyectos
[[Creación de proyectos.md]] - Iniciar proyectos Angular
- Angular CLI
- Workspace
- Estructura de carpetas
- Configuración inicial

### Componentes
[[Creación de componente Angular.md]] - Crear componentes robustos
- Anatomía de un componente
- Templates
- Estilos (encapsulación)
- Lifecycle hooks
- Change Detection

### Directivas y Pipes
[[Directives and Pipes.md]] - Extensibilidad de Angular
- Structural directives (*ngIf, *ngFor, *ngSwitch)
- Attribute directives
- Custom directives
- Built-in pipes
- Custom pipes

### Mejores Prácticas
[[Forma Oficial.md]] - Angular Style Guide
- Naming conventions
- Organización de código
- Module structure
- Service patterns
- Best practices

### Features Modernas
[[Signals.md]] - Señales (Angular 17+)
- Reactive primitive
- Change detection mejorada
- Computed signals
- Effects
- Vs RxJS

### Guías Avanzadas
[[Guías.md]] - Patrones y técnicas avanzadas
- Routing
- State management
- RxJS patterns
- Performance
- Testing

### Build Tools

#### Vite
[[Utilizando Vite.md]] - Usar Vite con Angular
- Setup
- HMR
- Optimizaciones
- Comparación con Webpack

#### Bun
[[Utilizando Bun.md]] - Runtime alternativo
- Bun runtime
- Package manager
- Build performance
- Ventajas y limitaciones

---

## 🔑 Conceptos Clave

### Signals (Angular 17+)
```typescript
const count = signal(0);
const doubled = computed(() => count() * 2);

effect(() => console.log(doubled())); // Auto-track
```

### Inyección de Dependencias
```typescript
@Injectable()
export class UserService {
  constructor(private http: HttpClient) {}
}

@Component({
  constructor(private userService: UserService) {}
})
```

### Observables (RxJS)
```typescript
this.users$ = this.userService.getUsers()
  .pipe(
    map(users => users.filter(u => u.active)),
    catchError(err => of([]))
  );
```

---

## 📊 Arquitectura Típica

```
angular-app/
├── src/
│   ├── app/
│   │   ├── components/    # Componentes reutilizables
│   │   ├── pages/         # Páginas/rutas
│   │   ├── services/      # Servicios
│   │   ├── models/        # Tipos e interfaces
│   │   ├── pipes/         # Custom pipes
│   │   ├── directives/    # Custom directives
│   │   └── app.routes.ts  # Routing
│   ├── assets/            # Imágenes, fonts
│   └── main.ts
├── angular.json           # Configuración
├── tsconfig.json
└── package.json
```

---

## 🔗 Enlaces Relacionados

- [[../React/_README.md|React (Alternativa)]]
- [[../TypeScript/_README.md|TypeScript]]
- [[../Web-Conceptos/_README.md|Web Concepts]]
- [[../../README|Mapa de Stack]]

---

## Control de versiones

| Versión | Fecha | Autor | Cambios |
|---------|-------|-------|---------|
| 2.0.0 | 2026-03-30 | Daniel | Reorganización y enriquecimiento |
| 1.0.0 | 2026-03-29 | Daniel | Creación inicial |
