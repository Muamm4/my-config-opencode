# React Component Architect Skill

## Overview

This skill provides comprehensive guidance for building production-ready React components with a focus on advanced design patterns, performance optimization, accessibility, and state management in Inertia.js contexts. It covers the techniques used in modern design systems like Radix UI, shadcn/ui, and enterprise Laravel applications.

**Target Use Cases:** Building reusable component libraries, design systems, complex business components, Inertia.js-powered Laravel applications.

**Prerequisites:** React 18+, TypeScript, familiarity with Hooks.

---

## 1. Advanced React Design Patterns

### 1.1 Compound Components Pattern

#### Core Concept

Compound components are a set of related components that work together to share implicit state. The parent manages state while child components consume it declaratively through React Context. This pattern provides flexibility without prop drilling—users compose UI naturally like native HTML elements like `<select>` and `<option>`.

**When to Use:** Components with multiple related parts that need coordinated state (Tabs, Accordion, Select, Card, Modal).

**When to Avoid:** Simple isolated components with no shared state.

#### Implementation Pattern with TypeScript

```typescript
// types.ts
interface AccordionContextValue {
  allowMultiple?: boolean;
  activeItems: string[];
  toggleItem: (id: string) => void;
}

// AccordionContext.tsx
import { createContext, useContext } from 'react';

interface AccordionContextValue {
  allowMultiple?: boolean;
  activeItems: string[];
  toggleItem: (id: string) => void;
}

const AccordionContext = createContext<AccordionContextValue | null>(null);

const useAccordionContext = () => {
  const context = useContext(AccordionContext);
  if (!context) {
    throw new Error('Accordion subcomponents must be used within Accordion component');
  }
  return context;
};

// Accordion.tsx
import { useState, Children, isValidElement, cloneElement } from 'react';

interface AccordionProps {
  allowMultiple?: boolean;
  defaultValue?: string[];
  children: React.ReactNode;
  className?: string;
  onValueChange?: (value: string[]) => void;
}

export function Accordion({ allowMultiple, defaultValue = [], children, className, onValueChange }: AccordionProps) {
  const [activeItems, setActiveItems] = useState<string[]>(defaultValue);

  const toggleItem = (id: string) => {
    setActiveItems((prev) => {
      const isOpen = prev.includes(id);
      let newValue: string[];
      
      if (isOpen) {
        newValue = prev.filter((item) => item !== id);
      } else if (allowMultiple) {
        newValue = [...prev, id];
      } else {
        newValue = [id];
      }
      
      onValueChange?.(newValue);
      return newValue;
    });
  };

  // Clone children with context values
  const childrenWithContext = Children.map(children, (child) => {
    if (!isValidElement<React.ComponentProps<any>>(child)) return child;
    return cloneElement(child, { 
      activeItems, 
      toggleItem,
      allowMultiple 
    });
  });

  return (
    <AccordionContext.Provider value={{ allowMultiple, activeItems, toggleItem }}>
      <div className={className} data-radix-accordion>
        {childrenWithContext}
      </div>
    </AccordionContext.Provider>
  );
}

// AccordionItem.tsx
interface AccordionItemProps {
  value: string;
  children: React.ReactNode;
  activeItems?: string[];
}

export function AccordionItem({ value, children }: AccordionItemProps) {
  const { activeItems } = useAccordionContext();
  const isOpen = activeItems?.includes(value);
  
  return (
    <div data-state={isOpen ? 'open' : 'closed'}>
      {Children.map(children, (child) => {
        if (!isValidElement<React.ComponentProps<any>>(child)) return child;
        
        // Pass item-specific props based on child type
        if (child.type === AccordionTrigger) {
          return cloneElement(child, { 
            value,
            'data-state': isOpen ? 'open' : 'closed'
          });
        }
        if (child.type === AccordionContent) {
          return cloneElement(child, { 
            value,
            'data-state': isOpen ? 'open' : 'closed'
          });
        }
        return child;
      })}
    </div>
  );
}

// AccordionTrigger.tsx
interface AccordionTriggerProps {
  children: React.ReactNode;
  className?: string;
  value?: string;
}

export function AccordionTrigger({ children, className, value }: AccordionTriggerProps) {
  const { activeItems, toggleItem } = useAccordionContext();
  const itemValue = value || '';
  const isOpen = activeItems?.includes(itemValue);

  return (
    <button
      type="button"
      onClick={() => toggleItem(itemValue)}
      aria-expanded={isOpen}
      className={className}
    >
      {children}
    </button>
  );
}

// AccordionContent.tsx
interface AccordionContentProps {
  children: React.ReactNode;
  className?: string;
  value?: string;
}

export function AccordionContent({ children, className, value }: AccordionContentProps) {
  const { activeItems } = useAccordionContext();
  const itemValue = value || '';
  const isOpen = activeItems?.includes(itemValue);

  return (
    <div 
      data-state={isOpen ? 'open' : 'closed'} 
      className={className}
      hidden={!isOpen}
    >
      {children}
    </div>
  );
}

// Export compound components as properties
Accordion.Item = AccordionItem;
Accordion.Trigger = AccordionTrigger;
Accordion.Content = AccordionContent;
```

#### Usage

```typescript
function App() {
  return (
    <Accordion allowMultiple defaultValue={['item-1']}>
      <Accordion.Item value="item-1">
        <Accordion.Trigger>What is this?</Accordion.Trigger>
        <Accordion.Content>It's a compound component pattern.</Accordion.Content>
      </Accordion.Item>
      <Accordion.Item value="item-2">
        <Accordion.Trigger>Why use it?</Accordion.Trigger>
        <Accordion.Content>Flexible, declarative API without prop drilling.</Accordion.Content>
      </Accordion.Item>
    </Accordion>
  );
}
```

#### Trade-offs

| Aspect | Benefit | Drawback |
|--------|---------|----------|
| API Design | Flexible, declarative composition | Higher initial setup |
| TypeScript | Requires advanced patterns | More complex type definitions |
| Testing | Subcomponents testable in isolation | More test cases |
| Tree-shaking | Depends on export strategy | Can affect bundle if not careful |

#### Naming Strategies

**Dot Notation (Accordion.Trigger):** Better organization for large design systems with many component families. Signals clear namespace relationship.

**Separate Exports (AccordionTrigger):** Simpler TypeScript support, better tree-shaking, easier testing. Prefer for small-to-medium projects.

---

### 1.2 Render Props Pattern

#### Core Concept

The render props pattern shares code between components by passing a function as a prop (traditionally called `render` or using `children` as a function) that returns a React element. The component controls rendering logic while the consumer controls the output.

**When to Use:** You own the logic but want to give users complete control over visual rendering. Virtualization libraries, data fetching abstractions, complex state machines.

**When to Avoid:** When compound components or custom hooks achieve the same goal more cleanly.

#### Implementation Pattern

```typescript
// MouseTracker.tsx
import { useState, useCallback } from 'react';

interface MousePosition {
  x: number;
  y: number;
}

interface RenderProps {
  x: number;
  y: number;
}

interface MouseTrackerProps {
  render: (position: RenderProps) => React.ReactNode;
}

export function MouseTracker({ render }: MouseTrackerProps) {
  const [position, setPosition] = useState<MousePosition>({ x: 0, y: 0 });

  const handleMouseMove = useCallback((event: React.MouseEvent) => {
    setPosition({ x: event.clientX, y: event.clientY });
  }, []);

  return (
    <div
      onMouseMove={handleMouseMove}
      style={{ width: '100%', height: '100%' }}
    >
      {render(position)}
    </div>
  );
}

// Usage - control rendering completely
function App() {
  return (
    <MouseTracker
      render={(position) => (
        <div style={{ position: 'absolute', left: position.x, top: position.y }}>
          Mouse at {position.x}, {position.y}
        </div>
      )}
    />
  );
}
```

#### Modern Alternative: Custom Hooks

The render props pattern has largely been replaced by custom hooks, which provide better TypeScript inference and cleaner code.

```typescript
// useMousePosition.ts
import { useState, useCallback } from 'react';

interface MousePosition {
  x: number;
  y: number;
}

export function useMousePosition() {
  const [position, setPosition] = useState<MousePosition>({ x: 0, y: 0 });

  const handleMouseMove = useCallback((event: MouseEvent) => {
    setPosition({ x: event.clientX, y: event.clientY });
  }, []);

  return { position, handleMouseMove };
}

// Usage
function App() {
  const { position } = useMousePosition();
  return (
    <div>Mouse at {position.x}, {position.y}</div>
  );
}
```

---

### 1.3 Higher-Order Components (HOCs)

#### Core Concept

A Higher-Order Component is a function that takes a component and returns a new enhanced component. It wraps the original component to add functionality without modifying it directly. HOCs are useful for cross-cutting concerns like authentication, logging, or theming.

**When to Use:** Applying the same concern to many components—auth gates, route-level wrappers, analytics tracking, loading states.

**When to Avoid:** Most cases where hooks suffice. HOCs can lead to "wrapper hell" with nested components.

#### Implementation Pattern

```typescript
// withAuth.tsx
import { useEffect, ComponentType, useState } from 'react';

interface User {
  id: string;
  name: string;
}

interface AuthState {
  user: User | null;
  isLoading: boolean;
}

function useAuth(): AuthState {
  // Your auth logic
  return { user: null, isLoading: false };
}

function withAuth<P extends object>(WrappedComponent: ComponentType<P>) {
  function AuthenticatedComponent(props: P) {
    const { user, isLoading } = useAuth();

    if (isLoading) return <div>Loading...</div>;
    if (!user) return <div>Please log in</div>;

    return <WrappedComponent {...props} />;
  }

  AuthenticatedComponent.displayName = `withAuth(${WrappedComponent.displayName ?? WrappedComponent.name ?? 'Component'})`;
  return AuthenticatedComponent;
}

// withIntersectionObserver.tsx
import { useEffect, useRef } from 'react';

function withIntersectionObserver<P extends { id?: string }>(WrappedComponent: ComponentType<P>, options?: IntersectionObserverInit) {
  function TrackedComponent(props: P) {
    const ref = useRef<HTMLDivElement>(null);
    const reported = useRef(false);

    useEffect(() => {
      const element = ref.current;
      if (!element) return;

      const observer = new IntersectionObserver(
        ([entry]) => {
          if (entry.isIntersecting && !reported.current) {
            reported.current = true;
            console.log('Element intersected', { id: props.id });
          }
        },
        { threshold: 0.5, ...options }
      );

      observer.observe(element);
      return () => observer.disconnect();
    }, [props.id]);

    return (
      <div ref={ref}>
        <WrappedComponent {...props} />
      </div>
    );
  }

  TrackedComponent.displayName = `withIntersectionObserver(${WrappedComponent.displayName})`;
  return TrackedComponent;
}

// Usage
const ProtectedDashboard = withAuth(Dashboard);
const TrackedCard = withIntersectionObserver(Card, { threshold: 0.25 });
```

#### HOC Composition

```typescript
// Compose multiple HOCs
const EnhancedComponent = withAuth(withIntersectionObserver(withData(MyComponent), 'fetch'));

// Or use a compose utility
function compose<T>(...fns: ((arg: T) => T)[]) {
  return fns.reduce((f, g) => (arg) => g(f(arg)));
}

const composed = compose(
  withData('fetch'),
  withIntersectionObserver,
  withAuth
)(MyComponent);
```

---

## 2. Performance Optimization

### 2.1 When to Optimize

**Profile first, optimize second.** Use React DevTools Profiler to identify actual bottlenecks. Premature optimization adds complexity without measurable benefit. Focus optimization efforts where they matter most—frequently re-rendered components, large lists, expensive computations, context providers.

Common signs you need to optimize:
- Unnecessary re-renders visible in Profiler
- Dropped frames during scrolling
- Slow typing in input fields
- Heavy computations (>1ms) on every render

### 2.2 React.memo

`React.memo` prevents re-rendering when props are unchanged through shallow comparison. Use on heavy components that receive stable props but re-render due to parent changes.

```typescript
// Basic usage
import { memo } from 'react';

interface ChildProps {
  name: string;
  onClick: () => void;
}

const ChildComponent = memo(function ChildComponent({ name, onClick }: ChildProps) {
  console.log('Child rendered');
  return <button onClick={onClick}>{name}</button>;
});

// First render: logs "Child rendered"
// Parent re-renders without prop changes: skipped
// If onClick reference changes: re-renders
```

**Custom Comparator for complex props:**

```typescript
interface Props {
  user: { name: string; id: string; avatar: string };
}

const UserAvatar = memo(
  function UserAvatar({ user }: Props) {
    return <img src={user.avatar} alt={user.name} />;
  },
  (prevProps, nextProps) => prevProps.user.id === nextProps.user.id
);
```

### 2.3 useCallback

`useCallback` stabilizes function references to prevent child re-renders when passing functions to memoized components.

```typescript
import { useCallback, useState, memo } from 'react';

interface ButtonProps {
  onClick: () => void;
  label: string;
}

const Button = memo(function Button({ onClick, label }: ButtonProps) {
  console.log(`Button ${label} rendered`);
  return <button onClick={onClick}>{label}</button>;
});

function Parent() {
  const [count, setCount] = useState(0);

  // Without useCallback - new function every render, breaks memo
  // const handleClick = () => console.log('clicked');

  // With useCallback - stable reference
  const handleClick = useCallback(() => console.log('clicked'), []);

  return (
    <div>
      <Button onClick={handleClick} label="Click me" />
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

### 2.4 useMemo

`useMemo` caches expensive computation results to avoid recalculation on every render.

```typescript
import { useMemo } from 'react';

interface Project {
  id: string;
  name: string;
  status: 'active' | 'completed' | 'archived';
}

interface Filters {
  status?: string;
  search: string;
}

function ProjectList({ projects, filters }: { projects: Project[]; filters: Filters }) {
  const filteredProjects = useMemo(() => {
    return projects
      .filter((project) => {
        if (filters.status && project.status !== filters.status) return false;
        if (filters.search && !project.name.toLowerCase().includes(filters.search.toLowerCase())) {
          return false;
        }
        return true;
      })
      .sort((a, b) => a.name.localeCompare(b.name));
  }, [projects, filters.status, filters.search]);

  return (
    <ul>
      {filteredProjects.map((project) => (
        <li key={project.id}>{project.name}</li>
      ))}
    </ul>
  );
}
```

### 2.5 Combined Strategy

```typescript
import { useState, useCallback, useMemo, memo } from 'react';

// Memoized Child - only re-renders when data or onAction changes
interface ExpensiveChildProps {
  data: { id: string; label: string }[];
  onAction: (id: string) => void;
}

const ExpensiveChild = memo(function ExpensiveChild({
  data,
  onAction
}: ExpensiveChildProps) {
  // Expensive render logic
  return (
    <ul>
      {data.map((item) => (
        <li key={item.id}>
          {item.label}
          <button onClick={() => onAction(item.id)}>Action</button>
        </li>
      ))}
    </ul>
  );
});

function Parent() {
  const [count, setCount] = useState(0);

  // Memoize derived data - doesn't change when parent re-renders
  const processedData = useMemo(() => {
    return [
      { id: '1', label: 'Item 1' }, 
      { id: '2', label: 'Item 2' }
    ];
  }, []);

  // Stable callback
  const handleAction = useCallback((id: string) => {
    console.log('Action:', id);
  }, []);

  // count changes but processedData and handleAction stay stable
  // ExpensiveChild won't re-render unnecessarily
  return (
    <div>
      <ExpensiveChild data={processedData} onAction={handleAction} />
      <button onClick={() => setCount(count + 1)}>Count: {count}</button>
    </div>
  );
}
```

### 2.6 Context Optimization

React Context can cause widespread re-renders if not properly structured. Key strategies:

- Split large contexts into focused, smaller contexts
- Memoize values passed to context
- Use multiple small contexts instead of one large context

```typescript
// Problem: Large context causes unnecessary re-renders
interface AppState {
  user: User;
  theme: 'light' | 'dark';
  notifications: Notification[];
  // ... many more
}

const AppContext = createContext<AppState>(null);

// Any state change re-renders ALL consumers
export function App() {
  const [state, setState] = useState({ theme: 'light' });
  // Theme change triggers re-render of every context consumer
  
  return <AppContext.Provider value={state}>...</AppContext.Provider>;
}

// Solution: Multiple focused contexts
const ThemeContext = createContext<'light' | 'dark'>('light');
const NotificationsContext = createContext<Notification[]>([]);

// Only ThemeContext consumers re-render on theme change
export function App() {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  const [notifications, setNotifications] = useState<Notification[]>([]);

  return (
    <ThemeContext.Provider value={theme}>
      <NotificationsContext.Provider value={notifications}>
        {/* Theme-only components won't re-render on notification changes */}
      </NotificationsContext.Provider>
    </ThemeContext.Provider>
  );
}
```

### 2.7 Guidelines Summary

| Tool | Purpose | Use When |
|------|---------|----------|
| `React.memo` | Skip re-renders when props unchanged | Heavy components with frequent parent re-renders |
| `useMemo` | Cache computed values | Expensive operations (>1ms), derived data |
| `useCallback` | Stable function references | Functions passed to memoized children |
| Context Splitting | Granular re-renders | Multiple unrelated state values |

**Don't:**
- Memoize everything—measure first
- Use for cheap calculations (memoization overhead exceeds benefit)
- Forget dependencies in dependency arrays
- Use without verifying actual performance gains

---

## 3. Headless UI Patterns (Radix UI)

### 3.1 Core Concept

Headless UI component libraries provide behavior, accessibility, and keyboard interactions without visual styles. You bring your own styling (Tailwind, CSS Modules, etc.). Examples include Radix UI Primitives and React Aria.

**Radix UI Philosophy:**
- Accessible: Components follow WAI-ARIA patterns automatically
- Unstyled: Complete control over appearance
- Composable: Direct DOM access with 1:1 mapping
- Uncontrolled by default, but can be controlled

### 3.2 Radix Dialog Implementation

```typescript
import * as Dialog from '@radix-ui/react-dialog';
import { useState } from 'react';

// Radix handles: role="dialog", aria-modal, focus trap, escape to close
// body scroll lock, restores focus to trigger

function Modal() {
  const [open, setOpen] = useState(false);

  return (
    <Dialog.Root open={open} onOpenChange={setOpen}>
      <Dialog.Trigger asChild>
        <button>Open modal</button>
      </Dialog.Trigger>
      <Dialog.Portal>
        <Dialog.Overlay className="fixed inset-0 bg-black/50" />
        <Dialog.Content className="fixed top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 bg-white p-6 rounded-lg">
          <Dialog.Title>Edit profile</Dialog.Title>
          <Dialog.Description>Make changes to your profile here.</Dialog.Description>
          <form>
            <input type="text" placeholder="Name" className="border rounded px-3 py-2 w-full" />
            <button type="submit" className="mt-4 bg-blue-600 text-white px-4 py-2 rounded">
              Save
            </button>
          </form>
          <Dialog.Close asChild>
            <button aria-label="Close" className="absolute top-4 right-4">
              ×
            </button>
          </Dialog.Close>
        </Dialog.Content>
      </Dialog.Portal>
    </Dialog.Root>
  );
}
```

### 3.3 Radix Dropdown Menu

```typescript
import * as DropdownMenu from '@radix-ui/react-dropdown-menu';

// Handles: arrow-key navigation, typeahead search, role="menu", portal
// collision detection, focus management

function MyMenu() {
  return (
    <DropdownMenu.Root>
      <DropdownMenu.Trigger asChild>
        <button>Open menu</button>
      </DropdownMenu.Trigger>
      <DropdownMenu.Portal>
        <DropdownMenu.Content className="bg-white shadow-lg p-1 rounded min-w-[150px]" sideOffset={5}>
          <DropdownMenu.Item className="p-2 hover:bg-gray-100 cursor-pointer outline-none">
            Profile
          </DropdownMenu.Item>
          <DropdownMenu.Item className="p-2 hover:bg-gray-100 cursor-pointer outline-none">
            Settings
          </DropdownMenu.Item>
          <DropdownMenu.Separator className="h-[1px] bg-gray-200 my-1" />
          <DropdownMenu.Item className="p-2 hover:bg-gray-100 cursor-pointer outline-none text-red-600">
            Log out
          </DropdownMenu.Item>
        </DropdownMenu.Content>
      </DropdownMenu.Portal>
    </DropdownMenu.Root>
  );
}
```

### 3.4 Radix Tabs (Compound Component Integration)

```typescript
import * as Tabs from '@radix-ui/react-tabs';

function Settings() {
  return (
    <Tabs.Root defaultValue="account">
      <Tabs.List className="flex border-b">
        <Tabs.Trigger 
          value="account" 
          className="px-4 py-2 data-[state=active]:border-b-2 data-[state=active]:border-blue-600"
        >
          Account
        </Tabs.Trigger>
        <Tabs.Trigger 
          value="password" 
          className="px-4 py-2 data-[state=active]:border-b-2 data-[state=active]:border-blue-600"
        >
          Password
        </Tabs.Trigger>
      </Tabs.List>
      <Tabs.Content value="account" className="p-4">
        <p>Account settings...</p>
      </Tabs.Content>
      <Tabs.Content value="password" className="p-4">
        <p>Password settings...</p>
      </Tabs.Content>
    </Tabs.Root>
  );
}
```

### 3.5 Comparison: Radix UI vs React Aria

| Aspect | Radix UI | React Aria (Adobe) |
|--------|---------|-----------|
| Styling | Unstyled, your CSS | Unstyled, your CSS |
| ARIA Level | WCAG AA | WCAG AAA (stricter) |
| Date Picker | Not included | Full patterns |
| Virtual Cursor | Partial | Full iOS/Android |
| Bundle Size | Smaller | Larger |
| Use Case | Most applications | Strict compliance required |
| API | Component-based | Hooks-first (now components too) |

---

## 4. Accessibility (A11y)

### 4.1 Core Principles

Accessibility is built into component architecture, not added as an afterthought. Follow WAI-ARIA authoring practices.

### 4.2 ARIA Basics

**Roles:** What the element is (`role="button"`, `role="menu"`)

**Properties:** Additional information (`aria-label`, `aria-describedby`, `aria-expanded`)

**States:** Current state (`aria-checked`, `aria-selected`, `aria-disabled`)

### 4.3 Accessible Modal Pattern

```typescript
interface ModalProps {
  isOpen: boolean;
  onClose: () => void;
  title: string;
  children: React.ReactNode;
}

function Modal({ isOpen, onClose, title, children }: ModalProps) {
  // Handle escape key
  useEffect(() => {
    const handleEscape = (e: KeyboardEvent) => {
      if (e.key === 'Escape') onClose();
    };
    
    if (isOpen) {
      document.addEventListener('keydown', handleEscape);
      return () => document.removeEventListener('keydown', handleEscape);
    }
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  return (
    <div 
      role="dialog" 
      aria-modal="true" 
      aria-labelledby="modal-title"
      className="fixed inset-0 flex items-center justify-center"
    >
      <div className="fixed inset-0 bg-black/50" onClick={onClose} aria-hidden="true" />
      <div className="relative bg-white p-6 rounded-lg max-w-md z-10">
        <button onClick={onClose} aria-label="Close modal" className="absolute top-4 right-4">
          ×
        </button>
        <h2 id="modal-title" className="text-xl font-semibold mb-4">{title}</h2>
        {children}
      </div>
    </div>
  );
}
```

### 4.4 Accessible Form Validation

```typescript
interface FormFieldProps {
  label: string;
  error?: string;
  children: React.ReactNode;
}

function FormField({ label, error, children }: FormFieldProps) {
  const id = `field-${label.toLowerCase().replace(/\s+/g, '-')}`;

  return (
    <div className="mb-4">
      <label htmlFor={id} className="block text-sm font-medium mb-1">
        {label}
      </label>
      {children}
      {error && (
        <span role="alert" id={`${id}-error`} className="text-sm text-red-600">
          {error}
        </span>
      )}
    </div>
  );
}

// Usage with proper ARIA attributes
<FormField label="Email" error={errors.email}>
  <input
    id="field-email"
    aria-invalid={!!errors.email}
    aria-describedby={errors.email ? 'field-email-error' : undefined}
    type="email"
    className="w-full border rounded px-3 py-2"
  />
</FormField>
```

### 4.5 Keyboard Navigation Standards

| Component | Key | Action |
|-----------|-----|--------|
| Button | Enter / Space | Activate |
| Link | Enter | Navigate |
| Checkbox | Space | Toggle |
| Menu Item | Enter | Activate |
| Tab | Arrow keys | Navigate between tabs |
| Dialog | Escape | Close |
| Slider | Arrow keys | Adjust value |
| ComboBox | Arrow keys | Navigate options |

### 4.6 Accessible Color

- Minimum 4.5:1 contrast ratio for normal text
- 3:1 for large text (18pt+ or 14pt+ bold)
- Never rely on color alone—use icons or text labels

### 4.7 Testing A11y

- Use screen readers (NVDA, VoiceOver, JAWS)
- Test with keyboard only
- Run automated tools: axe-core, eslint-plugin-jsx-a11y
- Check WCAG 2.2 guidelines

---

## 5. State Management with Inertia.js

### 5.1 Inertia.js Context

Inertia.js connects Laravel server-side routing with React without building a separate API. Pages are React components rendered via `Inertia::render()` in Laravel controllers.

### 5.2 State Strategy by Complexity

| Complexity | Solution |
|------------|----------|
| Simple, static data from Laravel | Props only, no client state |
| Theme, auth, locale | Context API |
| Medium (cart, filters, preferences) | Zustand |
| Large, complex async workflows | Redux Toolkit |

### 5.3 Context API for Application State

```typescript
// AppContext.tsx
import { createContext, useContext, useState } from 'react';

interface AppContextValue {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const AppContext = createContext<AppContextValue | null>(null);

// Setup in app.tsx - receives shared data from Laravel HandleInertiaRequests
export function AppProvider({ 
  children, 
  initialTheme = 'light' 
}: { 
  children: React.ReactNode; 
  theme?: 'light' | 'dark' 
}) {
  const [theme, setTheme] = useState(initialTheme);

  const toggleTheme = () => {
    setTheme((t) => (t === 'light' ? 'dark' : 'light'));
  };

  return (
    <AppContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </AppContext.Provider>
  );
}

export function useApp() {
  const context = useContext(AppContext);
  if (!context) throw new Error('useApp must be used within AppProvider');
  return context;
}
```

### 5.4 Zustand for Interactive State

```typescript
// stores/cartStore.ts
import { create } from 'zustand';

interface CartItem {
  id: string;
  name: string;
  quantity: number;
  price: number;
}

interface CartState {
  items: CartItem[];
  addItem: (item: CartItem) => void;
  removeItem: (id: string) => void;
  updateQuantity: (id: string, quantity: number) => void;
  total: () => number;
  clearCart: () => void;
}

export const useCartStore = create<CartState>((set, get) => ({
  items: [],

  addItem: (item) => {
    set((state) => {
      const existing = state.items.find((i) => i.id === item.id);
      if (existing) {
        return {
          items: state.items.map((i) =>
            i.id === item.id ? { ...i, quantity: i.quantity + item.quantity } : i
          ),
        };
      }
      return { items: [...state.items, item] };
    });
  },

  removeItem: (id) => {
    set((state) => ({ items: state.items.filter((i) => i.id !== id) }));
  },

  updateQuantity: (id, quantity) => {
    set((state) => ({
      items: state.items.map((i) => (i.id === id ? { ...i, quantity } : i)),
    }));
  },

  total: () => get().items.reduce((sum, item) => sum + item.price * item.quantity, 0),

  clearCart: () => set({ items: [] }),
}));
```

### 5.5 Zustand with Initial Data from Inertia

```typescript
// stores/userStore.ts
import { create } from 'zustand';

interface User {
  id: string;
  name: string;
  email: string;
  avatar?: string;
}

interface UserState {
  user: User | null;
  isLoading: boolean;
  setUser: (user: User | null) => void;
  updateUser: (userData: Partial<User>) => void;
  logout: () => Promise<void>;
}

// Create store with optional initial data
export const useUserStore = create<UserState>((set) => ({
  user: null,
  isLoading: true,

  setUser: (user) => set({ user, isLoading: false }),

  updateUser: (userData) => {
    set((state) => ({
      user: state.user ? { ...state.user, ...userData } : null,
    }));
  },

  logout: async () => {
    // Call Laravel logout endpoint
    await fetch('/logout', { method: 'POST' });
    set({ user: null });
  },
}));

// In page component
import { usePage } from '@inertiajs/react';

export default function Dashboard() {
  const { props } = usePage<{ user: User }>();
  
  // Initialize store with server data
  useUserStore.getState().setUser(props.user);
  
  const { user } = useUserStore();
  
  return <div>Welcome, {user.name}</div>;
}
```

### 5.6 Redux Toolkit for Large Applications

```typescript
// features/cart/cartSlice.ts
import { createSlice, PayloadAction, createAsyncThunk } from '@reduxjs/toolkit';
import { router } from '@inertiajs/react';

interface CartItem {
  id: string;
  name: string;
  quantity: number;
  price: number;
}

interface CartState {
  items: CartItem[];
  status: 'idle' | 'loading' | 'succeeded' | 'failed';
  error: string | null;
}

const initialState: CartState = { items: [], status: 'idle', error: null };

// Async thunk that uses Inertia
export const syncCartToServer = createAsyncThunk(
  'cart/sync',
  async (items: CartItem[]) => {
    await router.post('/cart/sync', { items });
    return items;
  }
);

const cartSlice = createSlice({
  name: 'cart',
  initialState,
  reducers: {
    addItem: (state, action: PayloadAction<CartItem>) => {
      const existing = state.items.find((i) => i.id === action.payload.id);
      if (existing) {
        existing.quantity += action.payload.quantity;
      } else {
        state.items.push(action.payload);
      }
    },
    removeItem: (state, action: PayloadAction<string>) => {
      state.items = state.items.filter((i) => i.id !== action.payload);
    },
    clearCart: (state) => {
      state.items = [];
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(syncCartToServer.pending, (state) => {
        state.status = 'loading';
      })
      .addCase(syncCartToServer.fulfilled, (state) => {
        state.status = 'succeeded';
      })
      .addCase(syncCartToServer.rejected, (state, action) => {
        state.status = 'failed';
        state.error = action.error.message || 'Sync failed';
      });
  },
});

export const { addItem, removeItem, clearCart } = cartSlice.actions;
export default cartSlice.reducer;

// store.ts
import { configureStore } from '@reduxjs/toolkit';
import cartReducer from './features/cart/cartSlice';

export const store = configureStore({
  reducer: {
    cart: cartReducer,
  },
});

export type RootState = ReturnType<typeof store.getState>;
export type AppDispatch = typeof store.dispatch;
```

### 5.7 Choosing State Management

- **Context API:** Theme, auth, locale—static or rarely changing data. Avoid for high-frequency updates.
- **Zustand:** Most React/Inertia apps. Simple API, small bundle (~2KB), excellent TypeScript support.
- **Redux Toolkit:** Large teams (5+), complex debugging needs, middleware requirements, complex async workflows.

---

## 6. Component API Design

### 6.1 Principles

**1. Name Things Clearly**

```typescript
// Bad - cryptic abbreviations
<Button onClick={fn} dsg={4} var="primary" />

// Good - semantic, descriptive names
<Button onClick={handleSubmit} variant="primary" size="md" />
```

**2. Provide Sensible Defaults**

```typescript
interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'ghost' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
}

function Button({
  variant = 'primary',
  size = 'md',
  disabled = false
}: ButtonProps) {
  // Defaults mean fewer props for users to specify
  return <button disabled={disabled}>{children}</button>;
}
```

**3. Use Union Types for Enums**

```typescript
// Bad - easy to typo, no autocomplete
<Select mode="any string" />

// Good - type-safe, documented options
<Select mode="single" | "multiple" />
```

**4. Support Controlled and Uncontrolled**

```typescript
interface ToggleProps {
  // Controlled
  checked?: boolean;
  onCheckedChange?: (checked: boolean) => void;
  // Uncontrolled - use defaultValue
  defaultChecked?: boolean;
}

function Toggle({ checked, onCheckedChange, defaultChecked }: ToggleProps) {
  const [internalChecked, setInternalChecked] = useState(defaultChecked ?? false);
  const isControlled = checked !== undefined;
  const value = isControlled ? checked : internalChecked;

  const handleToggle = () => {
    const newValue = !value;
    if (!isControlled) setInternalChecked(newValue);
    onCheckedChange?.(newValue);
  };

  return <button onClick={handleToggle}>{value ? 'On' : 'Off'}</button>;
}
```

**5. Expose Refs for Imperative APIs**

```typescript
import { forwardRef, useImperativeHandle, useRef } from 'react';

interface InputRef {
  focus: () => void;
  blur: () => void;
}

interface InputProps {
  // ...
}

const Input = forwardRef<InputRef, InputProps>((props, ref) => {
  const inputRef = useRef<HTMLInputElement>(null);

  useImperativeHandle(ref, () => ({
    focus: () => inputRef.current?.focus(),
    blur: () => inputRef.current?.blur(),
  }));

  return <input ref={inputRef} {...props} />;
});

// Usage
function App() {
  const inputRef = useRef<InputRef>(null);
  
  return (
    <>
      <Input ref={inputRef} />
      <button onClick={() => inputRef.current?.focus()}>Focus Input</button>
    </>
  );
}
```

**6. Accept Render Functions for Flexibility**

```typescript
interface ListProps<T> {
  items: T[];
  renderItem: (item: T, index: number) => React.ReactNode;
}

function List<T>({ items, renderItem }: ListProps<T>) {
  return <>{items.map((item, index) => renderItem(item, index))}</>;
}

// Usage
<List items={users} renderItem={(user) => <div key={user.id}>{user.name}</div>} />
```

### 6.2 Prefer Composition Over Configuration

```typescript
// Bad - props soup
interface CardProps {
  title?: string;
  titleAlign?: 'left' | 'center' | 'right';
  titleColor?: string;
  image?: string;
  content?: ReactNode;
  footer?: ReactNode;
  // ... 20+ more props
}

// Good - compound components
function Card({ children, ...props }: { children: React.ReactNode }) {
  return <div {...props}>{children}</div>;
}

function CardHeader({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>;
}

function CardTitle({ children }: { children: React.ReactNode }) {
  return <h3>{children}</h3>;
}

function CardImage({ src, alt }: { src: string; alt?: string }) {
  return <img src={src} alt={alt} />;
}

function CardContent({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>;
}

function CardFooter({ children }: { children: React.ReactNode }) {
  return <div>{children}</div>;
}

Card.Header = CardHeader;
Card.Title = CardTitle;
Card.Image = CardImage;
Card.Content = CardContent;
Card.Footer = CardFooter;

// Usage - declarative and flexible
function Example() {
  return (
    <Card className="max-w-sm">
      <Card.Header>
        <Card.Title>Product Name</Card.Title>
      </Card.Header>
      <Card.Image src="/product.jpg" alt="Product" />
      <CardContent>
        <p>Product description goes here.</p>
      </CardContent>
      <Card.Footer>
        <button>Add to Cart</button>
      </Card.Footer>
    </Card>
  );
}
```

### 6.3 API Design Checklist

- [ ] Names are semantic and descriptive
- [ ] Defaults are sensible
- [ ] Types are strict and documented
- [ ] Supports controlled/uncontrolled patterns
- [ ] Refs exposed for imperative APIs
- [ ] TypeScript inference works well
- [ ] Common cases need minimal props

---

## 7. Patterns Quick Reference

### 7.1 Compound Components

```typescript
// Parent
interface ParentProps {
  defaultValue?: string;
  onChange?: (value: string) => void;
  children: React.ReactNode;
}

function Parent({ defaultValue, onChange, children }: ParentProps) {
  // State + Context Provider
  return <ParentContext.Provider>{children}</ParentContext.Provider>;
}

// Subcomponents - access via useContext(ParentContext)
Parent.Subcomponent = Subcomponent;
```

### 7.2 Polymorphic Components (asChild)

```typescript
import { Slot } from '@radix-ui/react-slot';

interface PolymorphicProps {
  asChild?: boolean;
  children: React.ReactNode;
}

function Button({ asChild, children }: PolymorphicProps) {
  const Comp = asChild ? Slot : 'button';
  return <Comp>{children}</Comp>;
}
```

### 7.3 Generic Component Patterns

```typescript
function List<T extends { id: string | number }>({
  items,
  renderItem
}: {
  items: T[];
  renderItem: (item: T) => React.ReactNode;
}) {
  return <>{items.map((item) => <div key={item.id}>{renderItem(item)}</div>)}</>;
}
```

---

## 8. Skill Usage

**Load this skill when:**
- Building reusable component libraries
- Creating design system components
- Implementing complex business components (modals, forms, data tables)
- Working with Laravel Inertia.js applications
- Needing accessible, keyboard-navigable interfaces
- Optimize React performance

**Related Patterns:**
- Compound Components for Tabs, Accordion, Select, Card
- Headless UI (Radix UI) for Modal, Dropdown, Dialog, Tabs
- Zustand for Cart, Filters, User Preferences
- Context API for Theme, Auth, Locale
- React.memo/useMemo/useCallback for Performance

**Performance workflow:**
1. Profile with React DevTools
2. Identify unnecessary re-renders
3. Apply memoization systematically
4. Verify improvement with profiling
5. Remove if not effective

---

## 9. Resources

- Radix UI Primitives: https://www.radix-ui.com/primitives
- React Aria: https://react-spectrum.com/react-aria
- Kent C. Dodds Advanced Patterns: https://epicreact.dev
- shadcn/ui: https://ui.shadcn.com
- WAI-ARIA Authoring Practices: https://www.w3.org/WAI-ARIA/apg
- Zustand: https://github.com/pmndrs/zustand
- Redux Toolkit: https://redux-toolkit.js.org
- React Performance: https://react.dev/reference/react/memo

---

*This skill was researched and created to provide comprehensive technical guidance for building production-ready React components with focus on advanced design patterns, performance optimization, accessibility, and state management in Inertia.js contexts.*