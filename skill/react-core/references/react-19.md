# React 19.2 Reference

A quick lookup for the major changes and new features in React 19.2.
*Data de snapshot: Abril 2026. Status: Stable (GA).*

## 1. Actions
- **Simplification**: No more manual `isPending` state for form submissions. React automatically handles the transition.
- **`useActionState`**: High-level hook for async actions. Returns `[state, dispatch, isPending]`. React manages `isPending` automatically — no manual `setLoading` needed. Perfect for forms where you need the last result and a loading indicator.
- **`useFormStatus`**: Allows child components (like a submit button) to access the parent form's status without prop drilling.



## 2. The `use` API
- A new API to read resources during render. Unlike Hooks, it can be used in loops and conditionals.
- **Promises**: React will suspend the component until the promise resolves. Best used for reading data that was initiated outside of render or passed from a server component.
- **Context**: `use(Context)` is more flexible than `useContext`. You can call it inside an `if` block, allowing conditional context consumption.

## 3. Server Components (RSC)
- Components that execute only on the server, sending a serialized tree to the client.
- **Directives**:
  - `'use client'`: Marks the boundary for client-side interactivity.
  - `'use server'`: Marks a function (Action) as a **Server Function** that can be called from the client but runs on the server.
- **CRITICAL**: There is no directive for Server Components. `"use server"` is ONLY for Server Functions.

### 3.1 Best Practice: Moving Interactivity to the Leaves
To add interactivity to Server Components, compose them with Client Components. **Avoid making high-level wrappers Client Components.**

#### ❌ Anti-pattern (Global 'use client')
```tsx
'use client'; // Turns everything below into Client Components
export default function Wrapper({ children }) {
  const [isOpen, setIsOpen] = useState(false);
  return <div><button onClick={() => setIsOpen(!isOpen)}>Toggle</button>{children}</div>;
}
```

#### ✅ Best Practice (Composition)
```tsx
// Wrapper.tsx (Server Component - no directive)
import { InteractiveNav } from './InteractiveNav';
export default function Wrapper({ children }) {
  return <div><InteractiveNav />{children}</div>;
}

// InteractiveNav.tsx (Client Component)
'use client';
export function InteractiveNav() {
  const [isOpen, setIsOpen] = useState(false);
  return <button onClick={() => setIsOpen(!isOpen)}>Toggle</button>;
}
```


## 4. Ref Improvements
- **Ref as a Prop**: No longer need `forwardRef` in most cases. Just pass `ref` as a prop like any other.
- **Cleanup Functions**: `useRef` callbacks can now return a cleanup function.

### Example: Ref as a Prop (TypeScript)
```tsx
import { Ref } from 'react';

function CustomButton({ ref, label, ...props }: { ref?: Ref<HTMLButtonElement>, label: string }) {
  return <button ref={ref} {...props}>{label}</button>;
}

// Parent Usage
const btnRef = useRef<HTMLButtonElement>(null);
<CustomButton ref={btnRef} label="Focus Me" />
```

## 5. New Hooks (19.2+)
- **`useEffectEvent`**: Stabilized hook to extract non-reactive logic from Effects. Prevents stale closures without adding dependencies.

## 6. Better Metadata Support
- Native support for `<title>`, `<meta>`, and `<link>` tags anywhere in the tree. No more `react-helmet`.
- React 19 handles de-duplication and hoisting of metadata tags automatically.

## 7. New Components (19.2+)
- **`<Activity>`**: Control active/inactive state of component trees to manage resources (Offscreen).
- **`<ViewTransition>`**: Integration with the Browser View Transitions API.

## 8. `React.cache()` (Concurrent-Proof)
- **Purpose**: Deduplicate asynchronous data fetching in Server Components.
- **Note**: This is primarily used in framework-specific RSC environments to deduplicate shared database queries or API calls within a single request.

---
### 🔗 Contexto & Relacionados
- **Reference**: [Hooks Reference](../references/hooks.md)
- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
