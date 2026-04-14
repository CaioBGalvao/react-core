# React 19.2 Reference

A quick lookup for the major changes and new features in React 19.2.
*Data de snapshot: Abril 2026. Status: Stable (GA).*

## 1. Async Actions

React 19 revolutionizes how we handle form submission and async state.

### `<form action={...}>`

Pass an async function directly to the `action` prop of a `<form>`. React manages the "pending" state and resets the form automatically.

### `useActionState`

Replaces the manual `setLoading` / `setError` pattern. Perfect for forms where you need the last result and a loading indicator.

### `useFormStatus`

Get pending status of a parent form from a child component.

```tsx
const { pending } = useFormStatus();
```

### `useOptimistic`

Show the "final" state immediately while the async request is still running.

## 2. The `use` Hook

Read resources (like a Promise or Context) directly in render.

```tsx
const data = use(fetchPromise);
```

- **Unique**: Unlike other hooks, `use` can be called inside loops and conditional statements.
- **Promises**: React will suspend the component until the promise resolves. Best used for reading data that was initiated outside of render or passed from a server component.
- **Context**: `use(Context)` is more flexible than `useContext`. You can call it inside an `if` block, allowing conditional context consumption.

## 3. Server Components (RSC)

- Components that execute only on the server, sending a serialized tree to the client.
- **Directives**:
  - `'use client'`: Marks the boundary for client-side interactivity.
  - `'use server'`: Marks a function (Action) as a **Server Function** that can be called from the client but runs on the server.
- **CRITICAL**: There is no directive for Server Components. `"use server"` is ONLY for Server Functions (Actions).
- **The Boundary Marker (`'use client'`)**: Think of it as a "door" into the client-side module graph. Any module imported from a file with this directive is pulled into the client bundle.
- **Serialization Boundary**: When passing data from a Server Component to a Client Component, the data must be **serializable** (strings, numbers, simple objects/arrays). Functions, classes, and browser-specific objects (like `Event`) cannot cross this boundary.
- **Role of the Bundler**: The bundler (like Webpack or Turbopack) is the "glue" that allows these two environments to coexist. It ensures that server-only code (like database drivers) is never included in the browser bundle.

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

## 4. Simplified Ref handling

- `ref` is now a standard prop.
- **`forwardRef` is deprecated**. Just pass `ref` like any other prop.
- Standard ref cleanup: Returning a function from a ref-callback is now supported for cleanup logic.

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

## 6. Meta Tags & SEO

- React now supports rendering `<title>`, `<meta>`, and `<link>` tags anywhere in your component tree. They are automatically hoisted to the document `<head>`.

## 7. Resource Loading APIs

- **`preload`**: For high-priority assets.
- **`preinit`**: For scripts and styles.
- **`preconnect`**: Pre-warms connections.
- **`prefetchDNS`**: Lowest priority DNS resolution.
- **Benefit**: Reduces waterfalls by starting downloads as early as possible.

### Example: Preloading (RSC or Client)

```tsx
import { preload } from 'react-dom';

function MyPage() {
  preload('/fonts/big-font.woff2', { as: 'font' });
  return <main>...</main>;
}
```

## 8. `React.cache()` (Concurrent-Proof)

- **Purpose**: Deduplicate asynchronous data fetching in Server Components.
- **Note**: This is primarily used in framework-specific RSC environments to deduplicate shared database queries or API calls within a single request.

## 9. Security & Taint APIs

Prevent sensitive data from leaking into the Client.

- **`experimental_taintObjectReference(message, object)`**: Flags an object (like a user record or DB connection) so it cannot be serialized for the client.
- **`experimental_taintUniqueValue(message, lifetime, value)`**: Flags a specific value (like a password or API token).

### Example: Tainting a User Secret

```tsx
import { experimental_taintUniqueValue } from 'react';

export async function getUser(id) {
  const user = await db.users.get(id);
  experimental_taintUniqueValue(
    'Do not pass user password hashes to the client.',
    user,
    user.passwordHash
  );
  return user;
}
```

---

### 🔗 Contexto & Relacionados

- **Mindset**: [Architecture for Two Computers](./architecture-and-mindset.md)
- **Reference**: [Hooks Reference](./hooks.md)
