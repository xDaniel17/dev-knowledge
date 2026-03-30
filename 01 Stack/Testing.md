---
title: Testing Strategy
date: 2026-03-30
tags: [testing, jest, cypress, react, quality-assurance]
category: Testing
author: OpenCode
version: 1.0.0
status: active
---

# Testing Strategy - Jest, Cypress & React Testing Library

Comprehensive guide to testing modern JavaScript and React applications with unit, integration, and end-to-end tests.

## Table of Contents
1. [Testing Pyramid](#testing-pyramid)
2. [Jest Setup](#jest-setup)
3. [Unit Testing](#unit-testing)
4. [React Testing Library](#react-testing-library)
5. [Integration Testing](#integration-testing)
6. [Cypress E2E Testing](#cypress-e2e-testing)
7. [Mocking Strategies](#mocking-strategies)
8. [Test Organization](#test-organization)
9. [Coverage Goals](#coverage-goals)
10. [Best Practices](#best-practices)

---

## Testing Pyramid

```
        E2E Tests (5%)
    Integration Tests (15%)
Unit & Component Tests (80%)
```

- **Unit Tests** (80%): Fast, focused tests for functions and components
- **Integration Tests** (15%): Test multiple units working together
- **E2E Tests** (5%): Full user flows through the application

---

## Jest Setup

### Installation

```bash
npm install --save-dev jest @types/jest ts-jest
npx jest --init
```

### jest.config.js

```javascript
module.exports = {
  preset: 'ts-jest',
  testEnvironment: 'jsdom',
  roots: ['<rootDir>/src'],
  testMatch: ['**/__tests__/**/*.ts?(x)', '**/?(*.)+(spec|test).ts?(x)'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
    '\\.(css|less|scss|sass)$': 'identity-obj-proxy',
  },
  setupFilesAfterEnv: ['<rootDir>/src/setupTests.ts'],
  collectCoverageFrom: [
    'src/**/*.{ts,tsx}',
    '!src/**/*.d.ts',
    '!src/index.tsx',
  ],
  coverageThreshold: {
    global: {
      branches: 75,
      functions: 75,
      lines: 75,
      statements: 75,
    },
  },
};
```

### setupTests.ts

```typescript
import '@testing-library/jest-dom';

// Mock window.matchMedia
Object.defineProperty(window, 'matchMedia', {
  writable: true,
  value: jest.fn().mockImplementation((query) => ({
    matches: false,
    media: query,
    onchange: null,
    addListener: jest.fn(),
    removeListener: jest.fn(),
    addEventListener: jest.fn(),
    removeEventListener: jest.fn(),
    dispatchEvent: jest.fn(),
  })),
});
```

---

## Unit Testing

### Testing Functions

```typescript
// src/utils/math.ts
export const add = (a: number, b: number): number => a + b;
export const subtract = (a: number, b: number): number => a - b;
export const multiply = (a: number, b: number): number => a * b;

// src/utils/__tests__/math.test.ts
import { add, subtract, multiply } from '../math';

describe('Math utilities', () => {
  describe('add', () => {
    it('should add two numbers', () => {
      expect(add(2, 3)).toBe(5);
    });

    it('should handle negative numbers', () => {
      expect(add(-5, 3)).toBe(-2);
      expect(add(-5, -3)).toBe(-8);
    });

    it('should handle zero', () => {
      expect(add(0, 5)).toBe(5);
      expect(add(5, 0)).toBe(5);
    });
  });

  describe('subtract', () => {
    it('should subtract two numbers', () => {
      expect(subtract(5, 3)).toBe(2);
    });
  });

  describe('multiply', () => {
    it('should multiply two numbers', () => {
      expect(multiply(3, 4)).toBe(12);
    });

    it('should return 0 when multiplied by 0', () => {
      expect(multiply(100, 0)).toBe(0);
    });
  });
});
```

### Testing Classes

```typescript
// src/services/UserService.ts
export class UserService {
  private users: Array<{ id: number; name: string }> = [];
  private idCounter = 0;

  addUser(name: string): number {
    const id = ++this.idCounter;
    this.users.push({ id, name });
    return id;
  }

  getUser(id: number) {
    return this.users.find((user) => user.id === id);
  }

  getAllUsers() {
    return [...this.users];
  }

  deleteUser(id: number): boolean {
    const index = this.users.findIndex((user) => user.id === id);
    if (index === -1) return false;
    this.users.splice(index, 1);
    return true;
  }
}

// src/services/__tests__/UserService.test.ts
import { UserService } from '../UserService';

describe('UserService', () => {
  let service: UserService;

  beforeEach(() => {
    service = new UserService();
  });

  it('should add a user', () => {
    const id = service.addUser('John');
    expect(id).toBe(1);
    expect(service.getUser(1)).toEqual({ id: 1, name: 'John' });
  });

  it('should get all users', () => {
    service.addUser('John');
    service.addUser('Jane');
    expect(service.getAllUsers()).toHaveLength(2);
  });

  it('should delete a user', () => {
    const id = service.addUser('John');
    expect(service.deleteUser(id)).toBe(true);
    expect(service.getUser(id)).toBeUndefined();
  });

  it('should return false when deleting non-existent user', () => {
    expect(service.deleteUser(999)).toBe(false);
  });
});
```

### Testing Async Functions

```typescript
// src/api/fetchUser.ts
export const fetchUser = async (id: number) => {
  const response = await fetch(`/api/users/${id}`);
  if (!response.ok) throw new Error('Failed to fetch user');
  return response.json();
};

// src/api/__tests__/fetchUser.test.ts
import { fetchUser } from '../fetchUser';

describe('fetchUser', () => {
  beforeEach(() => {
    jest.clearAllMocks();
  });

  it('should fetch a user successfully', async () => {
    const mockUser = { id: 1, name: 'John' };
    
    global.fetch = jest.fn().mockResolvedValueOnce({
      ok: true,
      json: jest.fn().mockResolvedValueOnce(mockUser),
    });

    const user = await fetchUser(1);
    expect(user).toEqual(mockUser);
  });

  it('should throw error on failed fetch', async () => {
    global.fetch = jest.fn().mockResolvedValueOnce({
      ok: false,
    });

    await expect(fetchUser(1)).rejects.toThrow('Failed to fetch user');
  });
});
```

---

## React Testing Library

### Setup

```bash
npm install --save-dev @testing-library/react @testing-library/jest-dom
```

### Testing Components

```typescript
// src/components/Button.tsx
import React from 'react';

interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

export const Button: React.FC<ButtonProps> = ({
  label,
  onClick,
  disabled = false,
}) => (
  <button onClick={onClick} disabled={disabled}>
    {label}
  </button>
);

// src/components/__tests__/Button.test.tsx
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from '../Button';

describe('Button', () => {
  it('should render with label', () => {
    render(<Button label="Click me" onClick={jest.fn()} />);
    expect(screen.getByRole('button', { name: /click me/i })).toBeInTheDocument();
  });

  it('should call onClick handler when clicked', () => {
    const handleClick = jest.fn();
    render(<Button label="Click" onClick={handleClick} />);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });

  it('should be disabled when disabled prop is true', () => {
    render(<Button label="Click" onClick={jest.fn()} disabled={true} />);
    expect(screen.getByRole('button')).toBeDisabled();
  });

  it('should not call onClick when disabled', () => {
    const handleClick = jest.fn();
    render(<Button label="Click" onClick={handleClick} disabled={true} />);
    
    fireEvent.click(screen.getByRole('button'));
    expect(handleClick).not.toHaveBeenCalled();
  });
});
```

### Testing Hooks

```typescript
// src/hooks/useCounter.ts
import { useState, useCallback } from 'react';

export const useCounter = (initial = 0) => {
  const [count, setCount] = useState(initial);

  const increment = useCallback(() => setCount((prev) => prev + 1), []);
  const decrement = useCallback(() => setCount((prev) => prev - 1), []);
  const reset = useCallback(() => setCount(initial), [initial]);

  return { count, increment, decrement, reset };
};

// src/hooks/__tests__/useCounter.test.ts
import { renderHook, act } from '@testing-library/react';
import { useCounter } from '../useCounter';

describe('useCounter', () => {
  it('should initialize with default value', () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });

  it('should initialize with custom value', () => {
    const { result } = renderHook(() => useCounter(10));
    expect(result.current.count).toBe(10);
  });

  it('should increment', () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });

    expect(result.current.count).toBe(1);
  });

  it('should decrement', () => {
    const { result } = renderHook(() => useCounter(5));
    
    act(() => {
      result.current.decrement();
    });

    expect(result.current.count).toBe(4);
  });

  it('should reset to initial value', () => {
    const { result } = renderHook(() => useCounter(10));
    
    act(() => {
      result.current.increment();
      result.current.increment();
    });

    act(() => {
      result.current.reset();
    });

    expect(result.current.count).toBe(10);
  });
});
```

### Testing with User Interactions

```typescript
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

describe('Form Component', () => {
  it('should handle user input', async () => {
    const user = userEvent.setup();
    
    render(
      <input placeholder="Enter name" />
    );

    const input = screen.getByPlaceholderText('Enter name');
    
    await user.type(input, 'John Doe');
    expect(input).toHaveValue('John Doe');
  });

  it('should submit form', async () => {
    const user = userEvent.setup();
    const handleSubmit = jest.fn();

    render(
      <form onSubmit={handleSubmit}>
        <input placeholder="Enter name" />
        <button type="submit">Submit</button>
      </form>
    );

    await user.type(screen.getByPlaceholderText('Enter name'), 'John');
    await user.click(screen.getByRole('button', { name: /submit/i }));

    expect(handleSubmit).toHaveBeenCalledTimes(1);
  });
});
```

---

## Integration Testing

### Testing Multiple Components

```typescript
// src/components/__tests__/LoginForm.integration.test.tsx
import { render, screen } from '@testing-library/react';
import userEvent from '@testing-library/user-event';
import { LoginForm } from '../LoginForm';

describe('LoginForm Integration', () => {
  it('should complete login flow', async () => {
    const user = userEvent.setup();
    const handleLogin = jest.fn();

    render(<LoginForm onLogin={handleLogin} />);

    const emailInput = screen.getByLabelText(/email/i);
    const passwordInput = screen.getByLabelText(/password/i);
    const submitButton = screen.getByRole('button', { name: /login/i });

    await user.type(emailInput, 'test@example.com');
    await user.type(passwordInput, 'password123');
    await user.click(submitButton);

    expect(handleLogin).toHaveBeenCalledWith({
      email: 'test@example.com',
      password: 'password123',
    });
  });

  it('should show error on failed login', async () => {
    const user = userEvent.setup();

    render(<LoginForm />);

    const submitButton = screen.getByRole('button', { name: /login/i });
    await user.click(submitButton);

    expect(
      screen.getByText(/email and password are required/i)
    ).toBeInTheDocument();
  });
});
```

---

## Cypress E2E Testing

### Installation & Setup

```bash
npm install --save-dev cypress @cypress/webpack-dev-server
npx cypress open
```

### cypress.config.ts

```typescript
import { defineConfig } from 'cypress';

export default defineConfig({
  e2e: {
    baseUrl: 'http://localhost:3000',
    viewportWidth: 1280,
    viewportHeight: 720,
    video: true,
    screenshotOnRunFailure: true,
  },
});
```

### Basic E2E Tests

```typescript
// cypress/e2e/auth.cy.ts
describe('Authentication Flow', () => {
  beforeEach(() => {
    cy.visit('/');
  });

  it('should login successfully', () => {
    cy.get('input[name="email"]').type('test@example.com');
    cy.get('input[name="password"]').type('password123');
    cy.get('button[type="submit"]').click();

    cy.url().should('include', '/dashboard');
    cy.get('[data-testid="welcome-message"]').should(
      'contain',
      'Welcome, test@example.com'
    );
  });

  it('should show error on invalid credentials', () => {
    cy.get('input[name="email"]').type('wrong@example.com');
    cy.get('input[name="password"]').type('wrongpassword');
    cy.get('button[type="submit"]').click();

    cy.get('[data-testid="error-message"]').should(
      'contain',
      'Invalid credentials'
    );
  });

  it('should logout successfully', () => {
    // Login first
    cy.login('test@example.com', 'password123');

    // Logout
    cy.get('[data-testid="logout-button"]').click();
    cy.url().should('include', '/login');
  });
});
```

### Custom Commands

```typescript
// cypress/support/commands.ts
Cypress.Commands.add('login', (email: string, password: string) => {
  cy.get('input[name="email"]').type(email);
  cy.get('input[name="password"]').type(password);
  cy.get('button[type="submit"]').click();
});

Cypress.Commands.add('createUser', (userData: any) => {
  cy.request('POST', '/api/users', userData).then((response) => {
    expect(response.status).to.eq(201);
    return response.body;
  });
});
```

---

## Mocking Strategies

### Mocking Modules

```typescript
// src/services/api.ts
export const getUser = async (id: number) => {
  const response = await fetch(`/api/users/${id}`);
  return response.json();
};

// src/components/__tests__/UserProfile.test.tsx
import { UserProfile } from '../UserProfile';

jest.mock('../services/api', () => ({
  getUser: jest.fn(),
}));

import * as api from '../services/api';

describe('UserProfile', () => {
  it('should display user data', async () => {
    const mockUser = { id: 1, name: 'John', email: 'john@example.com' };
    
    (api.getUser as jest.Mock).mockResolvedValueOnce(mockUser);

    render(<UserProfile userId={1} />);

    expect(await screen.findByText('John')).toBeInTheDocument();
    expect(screen.getByText('john@example.com')).toBeInTheDocument();
  });
});
```

### Mocking API Calls

```typescript
import { rest } from 'msw';
import { setupServer } from 'msw/node';

const server = setupServer(
  rest.get('/api/users/:id', (req, res, ctx) => {
    return res(
      ctx.json({ id: 1, name: 'John' })
    );
  })
);

beforeAll(() => server.listen());
afterEach(() => server.resetHandlers());
afterAll(() => server.close());

it('should fetch user from API', async () => {
  render(<UserProfile userId={1} />);
  expect(await screen.findByText('John')).toBeInTheDocument();
});
```

---

## Test Organization

### Folder Structure

```
src/
├── __tests__/
│   ├── unit/
│   │   ├── utils/
│   │   └── services/
│   ├── integration/
│   │   └── components/
│   └── e2e/
│       └── cypress/
├── components/
├── hooks/
├── services/
└── utils/
```

### Naming Convention

```
✓ Button.test.tsx       (unit test)
✓ Button.integration.test.tsx   (integration test)
✓ login.cy.ts          (e2e test - Cypress)
✓ auth.spec.ts         (alternative naming)
```

---

## Coverage Goals

### Coverage Thresholds

```javascript
// jest.config.js
coverageThreshold: {
  global: {
    branches: 75,
    functions: 75,
    lines: 75,
    statements: 75,
  },
  './src/components/': {
    branches: 90,
    functions: 90,
    lines: 90,
    statements: 90,
  },
}
```

### Generate Coverage Report

```bash
npm test -- --coverage

# Output
# ─────────────────────────────────
# File      | % Stmts | % Branch | % Funcs | % Lines
# ─────────────────────────────────
# All files |   82.5  |   78.3   |   85.1  |   82.1
```

---

## Best Practices

### Do's
- ✅ Test behavior, not implementation
- ✅ Use descriptive test names
- ✅ Follow AAA pattern (Arrange, Act, Assert)
- ✅ Keep tests isolated and independent
- ✅ Mock external dependencies
- ✅ Test error scenarios
- ✅ Use data-testid for resilient selectors

### Don'ts
- ❌ Test implementation details
- ❌ Use vague test names
- ❌ Create interdependent tests
- ❌ Test third-party libraries
- ❌ Ignore edge cases
- ❌ Skip error testing
- ❌ Use complex CSS selectors in tests

### Test Template

```typescript
describe('Component/Function', () => {
  // Setup
  beforeEach(() => {
    // Initialize
  });

  // Cleanup
  afterEach(() => {
    // Clean up
  });

  // Grouped tests
  describe('Feature', () => {
    it('should do X when Y', () => {
      // Arrange
      const input = { foo: 'bar' };

      // Act
      const result = functionUnderTest(input);

      // Assert
      expect(result).toEqual({ expected: 'value' });
    });
  });
});
```

---

## Related Documentation

- [[TypeScript.md]] - Type-safe testing
- [[Next.js/Server-Actions.md]] - Testing server actions
- [[React/Hooks-Catalogo.md]] - Testing custom hooks

---

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2026-03-30 | Initial testing strategy guide |

