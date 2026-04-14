# Performance and Rendering

This document consolidates references for performance and rendering.

## From: performance-optimization.md

### Performance Optimization

Patterns to keep your React application snappy and responsive.

#### 0. The Re-render Mental Model

Before optimizing, understand why React re-renders:
1.  **State changes**: The primary trigger.
2.  **Parent re-renders**: If a parent re-renders, all its children re-render by default (unless optimized).
3.  **Context changes**: All consumers of a Context re-render when the Provider's value changes.

#### 1. Composition over Memoization

The most effective way to optimize performance is through component structure, not manual memoization hooks. This is often referred to as avoiding the "Uphill Battle of Memoization".

-   **Lifting State Down**: If a piece of state is only used by a small part of the tree, move it _down_ so only that part re-renders.
-   **Component as Props (Slots)**: If a component wraps content, pass the content as `children` or a named prop. React handles these as "Elements". Since they were created *outside* the re-rendering component, React knows their identity hasn't changed and skips their re-render.
    -   *Rule of thumb:* If you can move state to a child or wrap static parts in `children`, do that before reaching for `useMemo`. Structural changes are more robust and less prone to "dependency rot" than hooks.

#### 2. Memoization (`useMemo` and `useCallback`)

- **`useMemo`**: Cache the result of an expensive calculation. Only recompute when dependencies change.
- **`useCallback`**: Cache a function definition. 
    - **The Useless `useCallback`**: `useCallback` is mostly useless if the component receiving the function is NOT wrapped in `React.memo`. If the child re-renders anyway because its parent did, the stable function identity doesn't save any performance; it only adds the overhead of the hook itself.
    - *Key Use Case:* Passing functions to memoized child components or as dependencies to other hooks (`useEffect`, `useMemo`).

#### 3. Optimizing Lists

- **Stable Keys**: Use unique, stable IDs. Never use indices if the list is dynamic.
- **Windowing/Virtualization**: For extremely large lists, only render what's visible on screen (use libraries like `react-window`).

#### 4. Non-Blocking Updates (`Transitions`)

- **`useTransition`**: Mark an update as a "transition" so it doesn't block the UI. React will handle it with lower priority and allow the user to keep interacting (e.g., typing) while the list filters in the background.
- **`useDeferredValue`**: Similar to transitions, but for a value. Useful when you get a prop and want to "defer" its impact on the UI.

#### 5. React Compiler

- Modern React (v19+) includes a compiler that automatically handles many of these optimizations. The goal is to write "pure" React code and let the compiler handle memoization.

#### 6. Identity Stability (Future-Proof)

- **Future-Proof**: `useMemo` is only a performance hint. React may discard it to free memory.
- **Stable Identity**: For values where identity/stability is required (e.g., subscription objects, stable IDs), use the functional initializer of `useState`. React guarantees this identity for the lifetime of the component (see [Future-Proof](../references/bulletproof-examples.md#9-future-proof)).


##### 🔗 Contexto & Relacionados

- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)
- **Reference**: [Hooks Reference](../references/hooks.md)


## From: reconciliation-keys.md

### Reconciliation & The Key Attribute

Understanding how React matches elements and preserves state across renders.

#### 0. The Reconciliation Algorithm (Fiber)

Reconciliation is the process by which React updates the DOM to match the element tree.

-   **Type Matching**: If a component type changes (e.g., `<div>` becomes `<span>`), React destroys the entire subtree and builds it from scratch.
-   **Component Identity**: React uses the component's position in the tree to determine identity. If a component stays in the same position, React preserves its state.

#### 1. The Role of the `key` Attribute

The `key` is a hint for the reconciliation algorithm. It gives a stable identity to a component, regardless of its position in the array.

-   **State Preservation**: When an item "moves" in a list (e.g., sorting), the `key` tells React "This is the same component as before, even if its index changed." React will move the DOM node and preserve its internal state (like input values or scroll position).
-   **The "Reset" Pattern**: You can use a `key` on a *single* component to force it to reset. Changing the `key` of a component tells React "This is a brand new identity," causing it to unmount the old instance and mount a fresh one with initial state.

#### 2. Best Practices for Keys

-   **Must be Stable**: Never use `Math.random()` or current timestamps as keys. This causes the component to unmount and remount on every render, destroying performance and state.
-   **Must be Unique (Among Siblings)**: Keys only need to be unique within the same array, not globally.
-   **Index as a Last Resort**: Only use the array index as a `key` if the list is **static** (never reordered, filtered, or added to). If the list is dynamic, using the index will lead to bugs (e.g., the wrong item being marked as "completed" in a todo list after a deletion).

#### 3. The "Key" Mistake: Inline Components

**NEVER** declare a component inside another component's render body.

```tsx
function Parent() {
  // ⛔ ANTI-PATTERN: Declaring Child inside Parent
  function Child() {
    return <div>I am a child</div>;
  }

  return (
    <div>
      <Child />
    </div>
  );
}
```

-   **The Problem**: On every render of `Parent`, a **new function** `Child` is created.
-   **The Consequence**: For React, a different function means a different "Type." It will unmount and remount the entire subtree on every single render of the parent, causing massive performance issues and loss of all state in the child.
-   **The Fix**: Always declare component functions at the top level or in separate files.

##### 🔗 Related
- **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)
- **Pattern**: [Performance Optimization](./performance-optimization.md)


## From: ssr-hydration.md

### SSR and Hydration

Understanding the synchronization between server-rendered HTML and client-side React, and the architectural distinction between Server and Client environments.

#### 0. The Two Worlds (Server vs Client)

Modern React (React 19+) spans two distinct environments. Understanding the difference between **Server-side Rendering (SSR)** and **React Server Components (RSC)** is critical:

*   **SSR (The "How"):** A technique to generate HTML from your components on the server so the user sees content immediately. This is about *initial visualization*.
*   **RSC (The "Where"):** An architectural choice about where the component's logic actually executes. Server Components run *only* on the server (or at build time) and never send their logic to the browser.
*   **The Paradigm Shift:** Think of the Server as the "Source of Truth" (secure, data-rich) and the Client as the "Source of Interaction" (stateful, responsive).

> [!TIP]
> Use Server Components for everything that doesn't need interactivity (hooks, state, browser APIs). This reduces the JavaScript bundle size significantly.

#### 1. What is Hydration?

Hydration is the process where React, running on the client, "attaches" to the static HTML already rendered by the server. React doesn't recreate the DOM; instead, it tries to adopt the existing DOM by matching it with its internal virtual DOM representation.

#### 2. The Flash of Wrong Content (FOUC)

A common issue in SSR is the "hydration flash." This happens when:
1. The server renders an initial state (e.g., `theme: 'light'`).
2. The browser displays this static HTML.
3. React hydrates on the client and detects a different value (e.g., `theme: 'dark'` from `localStorage`).
4. React "fixes" the UI, causing a visible jump or flash.

#### 3. Hydration Mismatches

React expects the first client-side render to match the server-side render exactly. Differences in:
- Dates (`new Date()`)
- Random values (`Math.random()`)
- Browser-only APIs (`window`, `localStorage`)
- Conditional rendering based on `window.innerWidth`

...all cause **Hydration Mismatches**, which are slow and can lead to broken UIs.

#### 4. The Correct Cycle

To avoid mismatches and flashes:
1. **Server Render**: Render a generic or "skeleton" state that is valid everywhere.
2. **Inline Script (Critical)**: Inject a synchronous `<script>` using `dangerouslySetInnerHTML` to set CSS variables or DOM attributes (like `dataset`) *before* React starts (see [Hydration-Proof](../references/bulletproof-examples.md#2-hydration-proof)). This ensures the visual style is applied before the first paint.
3. **Hydrate**: React attaches to the DOM.
4. **`useEffect`**: Use `useEffect` to read client-side-only values and update the state. Since `useEffect` runs after the UI is committed, it won't crash the server.

#### 5. Summary of Best Practices

- **Never call browser APIs during render**: Keep the function body "Server-Proof".
- **Use `useId()`**: Guaranteed to produce the same ID on server and client.
- **Client-only components**: Wrap components that heavily rely on browser APIs in a "Client Only" wrapper that only renders after mount.


##### 🔗 Contexto & Relacionados

- **Pattern**: [Bulletproof Patterns](../patterns/bulletproof.md)
- **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)
- **Reference**: [DOM APIs](../references/dom-apis.md)


