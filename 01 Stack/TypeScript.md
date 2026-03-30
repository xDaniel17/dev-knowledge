---
title: TypeScript Best Practices
date: 2026-03-30
tags: [typescript, frontend, backend, type-safety]
category: Developer-Tools
author: OpenCode
version: 1.0.0
status: active
---

# TypeScript - Best Practices & Advanced Patterns

TypeScript adds static typing to JavaScript, enabling better IDE support, error detection, and code maintainability. This guide covers best practices and advanced patterns for production applications.

## Table of Contents
1. [Setup & Configuration](#setup--configuration)
2. [Type Fundamentals](#type-fundamentals)
3. [Advanced Types](#advanced-types)
4. [Interfaces vs Types](#interfaces-vs-types)
5. [Generics](#generics)
6. [Utility Types](#utility-types)
7. [Error Handling](#error-handling)
8. [React Integration](#react-integration)
9. [API Type Safety](#api-type-safety)
10. [Best Practices](#best-practices)

---

## Setup & Configuration

### Install TypeScript

```bash
npm install --save-dev typescript
npx tsc --init
```

### Recommended tsconfig.json

```json
{
  "compilerOptions": {
    "target": "ES2020",
    "module": "ESNext",
    "lib": ["ES2020", "DOM", "DOM.Iterable"],
    "outDir": "./dist",
    "rootDir": "./src",
    "strict": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "resolveJsonModule": true,
    "declaration": true,
    "declarationMap": true,
    "sourceMap": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "noImplicitThis": true,
    "alwaysStrict": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": true,
    "moduleResolution": "node",
    "allowSyntheticDefaultImports": true
  },
  "include": ["src/**/*"],
  "exclude": ["node_modules", "dist"]
}
```

---

## Type Fundamentals

### Primitive Types

```typescript
// String
const name: string = 'John';

// Number
const age: number = 30;

// Boolean
const isActive: boolean = true;

// Any (avoid when possible)
let value: any = 'could be anything';

// Unknown (safer than any)
let unknown: unknown = 'something';
if (typeof unknown === 'string') {
  console.log(unknown.toUpperCase());
}

// Never (function that never returns)
const throwError = (message: string): never => {
  throw new Error(message);
};

// Void (function returns nothing)
const logMessage = (message: string): void => {
  console.log(message);
};
```

### Literal Types

```typescript
// String literal
type Status = 'active' | 'inactive' | 'pending';
const userStatus: Status = 'active';

// Number literal
type DiceRoll = 1 | 2 | 3 | 4 | 5 | 6;
const roll: DiceRoll = 3;

// Boolean literal
type FeatureFlag = true | false;

// Union types
type ID = string | number;
const userId: ID = 123;

// Template literal types
type EventName = `on_${string}`;
const eventName: EventName = 'on_click';
```

### Arrays & Tuples

```typescript
// Array
const numbers: number[] = [1, 2, 3];
const strings: Array<string> = ['a', 'b', 'c'];

// Tuple (fixed length with types)
const tuple: [string, number] = ['hello', 42];
const tuple2: [string, number, boolean] = ['test', 1, true];

// Optional tuple element
const optional: [string, number?] = ['hello'];

// Rest element in tuple
const variadic: [string, ...number[]] = ['test', 1, 2, 3];

// Readonly arrays
const readonlyArray: readonly string[] = ['a', 'b'];
// readonlyArray.push('c'); // Error!
```

---

## Advanced Types

### Union & Intersection Types

```typescript
// Union: one of several types
type Response = string | number | boolean;
const response: Response = 'success';

// Discriminated union (tagged unions)
type SuccessResponse = {
  status: 'success';
  data: object;
};

type ErrorResponse = {
  status: 'error';
  error: string;
};

type ApiResponse = SuccessResponse | ErrorResponse;

const handleResponse = (response: ApiResponse) => {
  if (response.status === 'success') {
    console.log(response.data);
  } else {
    console.log(response.error);
  }
};

// Intersection: combination of types
type Admin = {
  adminLevel: number;
};

type User = {
  userId: number;
  username: string;
};

type AdminUser = Admin & User;

const admin: AdminUser = {
  adminLevel: 5,
  userId: 1,
  username: 'admin_user',
};
```

### Conditional Types

```typescript
// Simple conditional
type IsString<T> = T extends string ? true : false;

type A = IsString<'hello'>; // true
type B = IsString<number>; // false

// Complex conditional with inference
type GetType<T> = T extends Array<infer U> ? U : T;

type ArrayElement = GetType<string[]>; // string
type NotArray = GetType<string>; // string

// Conditional distribution (mapped types)
type Flatten<T> = T extends Array<infer U> ? U : T;

type Str = Flatten<string[]>; // string
type Num = Flatten<number>; // number
```

### Type Guards

```typescript
// User-defined type guard
function isString(value: unknown): value is string {
  return typeof value === 'string';
}

const checkType = (value: unknown) => {
  if (isString(value)) {
    console.log(value.toUpperCase()); // TypeScript knows it's a string
  }
};

// Type guard for custom types
type User = { userId: number; username: string };
type Admin = { adminLevel: number };

const isAdmin = (user: User | Admin): user is Admin => {
  return 'adminLevel' in user;
};

const handleUser = (user: User | Admin) => {
  if (isAdmin(user)) {
    console.log(`Admin level: ${user.adminLevel}`);
  }
};
```

---

## Interfaces vs Types

### Key Differences

```typescript
// Interfaces are for object shapes (can extend)
interface User {
  id: number;
  name: string;
}

interface AdminUser extends User {
  adminLevel: number;
}

// Types are more flexible (can be unions, primitives, etc.)
type UserType = {
  id: number;
  name: string;
};

// Type unions
type UserOrGuest = User | { isGuest: true };

// Interfaces can be merged (declaration merging)
interface User {
  email?: string; // Added to existing interface
}

// Types cannot be merged
// type UserType = {...}; // Error! Already defined
```

### When to Use Each

```typescript
// Use Interface for:
// - Object contracts (classes implementing them)
// - Public API surfaces
// - Object-oriented patterns

interface Database {
  connect(): Promise<void>;
  query(sql: string): Promise<any[]>;
  disconnect(): Promise<void>;
}

class PostgresDB implements Database {
  async connect() {}
  async query(sql: string) {}
  async disconnect() {}
}

// Use Type for:
// - Unions and intersections
// - Primitive types
// - Complex type operations
// - React component props

type PageProps = {
  params: { id: string };
  searchParams: Record<string, string>;
};

type HandlersMap = {
  [key: string]: (data: unknown) => void;
};
```

---

## Generics

### Basic Generics

```typescript
// Generic function
function identity<T>(value: T): T {
  return value;
}

const stringValue = identity<string>('hello');
const numberValue = identity(42); // Type inferred

// Generic interface
interface Container<T> {
  value: T;
  getValue(): T;
  setValue(value: T): void;
}

const stringContainer: Container<string> = {
  value: 'hello',
  getValue() { return this.value; },
  setValue(value) { this.value = value; }
};
```

### Generic Constraints

```typescript
// Constrain T to specific type
function getLength<T extends { length: number }>(obj: T): number {
  return obj.length;
}

getLength('hello'); // ✓
getLength([1, 2, 3]); // ✓
getLength(123); // ✗ Error: number doesn't have length

// Key constraint
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { id: 1, name: 'John' };
const name = getProperty(user, 'name'); // ✓ 'John'
getProperty(user, 'email'); // ✗ Error: no such property

// Multiple constraints
interface Named {
  name: string;
}

interface Aged {
  age: number;
}

function mergeIdentities<T extends Named & Aged>(
  obj: T
): T {
  return obj;
}
```

### Advanced Generic Patterns

```typescript
// Generic class
class Repository<T> {
  private items: T[] = [];

  add(item: T): void {
    this.items.push(item);
  }

  getAll(): T[] {
    return [...this.items];
  }

  find(predicate: (item: T) => boolean): T | undefined {
    return this.items.find(predicate);
  }
}

const userRepo = new Repository<User>();
userRepo.add({ id: 1, name: 'John' });

// Generic with default type
type Result<T = unknown> = { data: T; error?: null } | { data: null; error: string };

const stringResult: Result<string> = { data: 'success' };
const unknownResult: Result = { data: 'anything' };
```

---

## Utility Types

### Partial & Required

```typescript
// Partial: make all properties optional
type PartialUser = Partial<User>;
// { id?: number; name?: string }

const updates: PartialUser = { name: 'Jane' };

// Required: make all properties required
type RequiredUser = Required<Partial<User>>;
// { id: number; name: string }

// Record: create object type with specific keys
type RolePermissions = Record<'admin' | 'user' | 'guest', string[]>;

const permissions: RolePermissions = {
  admin: ['read', 'write', 'delete'],
  user: ['read', 'write'],
  guest: ['read'],
};
```

### Pick & Omit

```typescript
// Pick: select specific properties
type UserPreview = Pick<User, 'id' | 'name'>;
// { id: number; name: string }

// Omit: exclude specific properties
type UserWithoutId = Omit<User, 'id'>;
// { name: string }

// Readonly: make properties readonly
type ReadonlyUser = Readonly<User>;

// ReadonlyArray
type ReadonlyStringArray = ReadonlyArray<string>;
```

### Exclude & Extract

```typescript
// Exclude: remove types from union
type NonString = Exclude<string | number | boolean, string>;
// number | boolean

// Extract: keep only matching types
type OnlyString = Extract<string | number | boolean, string>;
// string

// NonNullable: remove null/undefined
type NonNull<T> = Exclude<T, null | undefined>;
type StringOrNull = string | null | undefined;
type JustString = NonNull<StringOrNull>; // string
```

### ReturnType & Parameters

```typescript
// Get return type of function
function greet(): string {
  return 'hello';
}

type GreetReturn = ReturnType<typeof greet>; // string

// Get function parameters as tuple
type GreetParams = Parameters<typeof greet>; // []

function addNumbers(a: number, b: number): number {
  return a + b;
}

type AddParams = Parameters<typeof addNumbers>; // [number, number]
type AddReturn = ReturnType<typeof addNumbers>; // number
```

---

## Error Handling

### Error Types

```typescript
// Custom error class
class ValidationError extends Error {
  constructor(public field: string, message: string) {
    super(message);
    this.name = 'ValidationError';
  }
}

// Type-safe error handling
type Result<T, E> = 
  | { ok: true; value: T }
  | { ok: false; error: E };

const validateEmail = (email: string): Result<string, ValidationError> => {
  if (!email.includes('@')) {
    return {
      ok: false,
      error: new ValidationError('email', 'Invalid email format'),
    };
  }
  return { ok: true, value: email };
};

// Using Result type
const result = validateEmail('test@example.com');
if (result.ok) {
  console.log(result.value);
} else {
  console.error(result.error.message);
}
```

### Try-Catch with TypeScript

```typescript
// Typed error handling
async function safeOperation<T>(
  fn: () => Promise<T>
): Promise<Result<T, Error>> {
  try {
    const value = await fn();
    return { ok: true, value };
  } catch (error) {
    const message = error instanceof Error ? error.message : 'Unknown error';
    return { ok: false, error: new Error(message) };
  }
}

// Usage
const result = await safeOperation(async () => {
  const response = await fetch('/api/data');
  return response.json();
});

if (result.ok) {
  console.log(result.value);
} else {
  console.error(result.error.message);
}
```

---

## React Integration

### Component Props

```typescript
// Function component with props
interface ButtonProps {
  label: string;
  onClick: (e: React.MouseEvent<HTMLButtonElement>) => void;
  disabled?: boolean;
  variant?: 'primary' | 'secondary';
}

const Button: React.FC<ButtonProps> = ({
  label,
  onClick,
  disabled = false,
  variant = 'primary',
}) => (
  <button onClick={onClick} disabled={disabled} className={variant}>
    {label}
  </button>
);

// Children typing
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  children: React.ReactNode;
}

const Modal: React.FC<ModalProps> = ({
  isOpen,
  onClose,
  children,
}) => {
  if (!isOpen) return null;
  return <div onClick={onClose}>{children}</div>;
};
```

### Hooks with TypeScript

```typescript
import { useState, useCallback, useMemo } from 'react';

// useState with explicit types
const [count, setCount] = useState<number>(0);
const [name, setName] = useState<string>('');

// useCallback with types
const handleClick = useCallback<(event: React.MouseEvent) => void>(
  (event) => {
    setCount((prev) => prev + 1);
  },
  []
);

// useMemo with types
const computedValue = useMemo<number>(() => {
  return count * 2;
}, [count]);

// Custom hook with types
interface User {
  id: number;
  name: string;
}

const useUser = (id: number): User | null => {
  const [user, setUser] = useState<User | null>(null);

  useState(() => {
    const fetchUser = async () => {
      const response = await fetch(`/api/users/${id}`);
      const data: User = await response.json();
      setUser(data);
    };

    fetchUser();
  }, [id]);

  return user;
};
```

---

## API Type Safety

### API Response Types

```typescript
// Generic API response wrapper
interface ApiResponse<T> {
  status: 'success' | 'error';
  data?: T;
  error?: {
    code: string;
    message: string;
  };
  timestamp: string;
}

// Fetch with types
async function fetchUser(id: number): Promise<ApiResponse<User>> {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
}

// Usage with proper typing
const handleFetchUser = async () => {
  const response = await fetchUser(1);
  
  if (response.status === 'success' && response.data) {
    console.log(response.data.name);
  } else {
    console.error(response.error?.message);
  }
};
```

### Axios Integration

```typescript
import axios, { AxiosError } from 'axios';

// Create typed axios instance
const api = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
});

// Request/Response interceptors with types
api.interceptors.response.use(
  (response) => response,
  (error: AxiosError<{ message: string }>) => {
    const message = error.response?.data?.message || 'Unknown error';
    console.error(message);
    return Promise.reject(error);
  }
);

// Typed request
interface CreateUserPayload {
  email: string;
  name: string;
}

const createUser = (payload: CreateUserPayload): Promise<User> => {
  return api.post<User>('/users', payload).then((res) => res.data);
};
```

---

## Best Practices

### Do's
- ✅ Enable `strict` mode in tsconfig.json
- ✅ Use explicit return types for functions
- ✅ Leverage type inference when obvious
- ✅ Create reusable type definitions
- ✅ Use discriminated unions for complex types
- ✅ Prefer `unknown` over `any`
- ✅ Document complex types with comments

### Don'ts
- ❌ Use `any` type (use `unknown` instead)
- ❌ Skip type definitions for function parameters
- ❌ Overuse generics (keep it simple)
- ❌ Mix interfaces and types inconsistently
- ❌ Create overly complex type hierarchies
- ❌ Ignore TypeScript compiler warnings

### Code Organization

```typescript
// types/index.ts - Centralize type definitions
export interface User {
  id: number;
  name: string;
  email: string;
}

export interface Post {
  id: number;
  userId: number;
  title: string;
  content: string;
}

export type ApiResponse<T> = {
  status: 'success' | 'error';
  data: T | null;
};

// api/users.ts - API calls with types
import { User, ApiResponse } from '../types';

export const getUser = async (id: number): Promise<ApiResponse<User>> => {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
};

// components/UserCard.tsx - Usage
import { User } from '../types';

interface UserCardProps {
  user: User;
}

export const UserCard: React.FC<UserCardProps> = ({ user }) => (
  <div>
    <h2>{user.name}</h2>
    <p>{user.email}</p>
  </div>
);
```

---

## Related Documentation

- [[React/Hooks-Catalogo.md]] - React Hooks with TypeScript
- [[Next.js/Server-Actions.md]] - Server Actions type safety
- [[CLI-Reference.md]] - TypeScript CLI commands

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-30 | Initial TypeScript best practices guide |

