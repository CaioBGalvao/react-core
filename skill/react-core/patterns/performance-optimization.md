# Performance Optimization

Patterns to keep your React application snappy and responsive.

## 1. Avoid Unnecessary Re-renders

- **Lifting State Down**: If a piece of state is only used by a small part of the tree, move it _down_ so only that part re-renders.
- **Passing Components as Props**: If a component wraps static content, pass the content as `children`. React won't re-render `children` if the wrapper's state changes.

## 2. Memoization (`useMemo` and `useCallback`)

- **`useMemo`**: Cache the result of an expensive calculation. Only recompute when dependencies change.
- **`useCallback`**: Cache a function definition. Useful when passing functions to memoized child components to prevent them from re-rendering due to "new function" identity.

## 3. Optimizing Lists

- **Stable Keys**: Use unique, stable IDs. Never use indices if the list is dynamic.
- **Windowing/Virtualization**: For extremely large lists, only render what's visible on screen (use libraries like `react-window`).

## 4. Non-Blocking Updates (`Transitions`)

- **`useTransition`**: Mark an update as a "transition" so it doesn't block the UI. React will handle it with lower priority and allow the user to keep interacting (e.g., typing) while the list filters in the background.
- **`useDeferredValue`**: Similar to transitions, but for a value. Useful when you get a prop and want to "defer" its impact on the UI.

## 5. React Compiler

- Modern React (v19+) includes a compiler that automatically handles many of these optimizations. The goal is to write "pure" React code and let the compiler handle memoization.

## 6. Identity Stability (Future-Proof)

- **Future-Proof**: `useMemo` is only a performance hint. React may discard it to free memory.
- **Stable Identity**: For values where identity/stability is required (e.g., subscription objects, stable IDs), use the functional initializer of `useState`. React guarantees this identity for the lifetime of the component (see [Future-Proof](../references/bulletproof-examples.md#9-future-proof)).

---

### 🔗 Contexto & Relacionados

- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)
- **Reference**: [Hooks Reference](../references/hooks.md)
