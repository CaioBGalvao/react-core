# Hooks Reference

Technical details for standard and advanced React Hooks, focusing on optimal usage and avoiding common anti-patterns.

## Effect Hooks
- **`useEffect`**: Synchronize with an external system.
    - **Anti-pattern**: Using `useEffect` to transform data before rendering (calculate it directly).
    - **Anti-pattern**: Using `useEffect` to communicate between components (use state uplifting or a shared store).
    - **Gotcha**: Always provide a dependency array. Avoid including objects/arrays that are recreated on every render (use `useMemo`).
- **`useEffectEvent` (React 19.2+)**:
    - **Purpose**: Extract non-reactive logic from an Effect. Unlike a normal function inside an Effect, logic inside `useEffectEvent` **MUST NOT** be included in the dependency array. It always sees the latest values from the "commit" phase.
    - **Use Case**: Logging, analytics, or using state/props that should be "read" by the effect without triggering a re-sync.
    - **Rules**:
        - **MUST NOT** be included in any dependency array.
        - **CANNOT** be called during rendering.
        - **MUST ONLY** be called from inside Effects or other Effect Events.
        - **DO NOT** call from regular event handlers or pass as props to components/hooks.
        - Must be defined at the top level of the component.
- **`useLayoutEffect`**: Fires synchronously *before* the browser repaints.
    - **When to use**: Measuring layout (e.g., getting the height of a DOM element) before the user sees the content.
    - **Activity-Proof**: Use `useLayoutEffect` to clean up DOM side effects (like `<style>` tags) when a component tree is hidden by `<Activity>` (see [Activity-Proof](./bulletproof-examples.md#8-activity-proof)).

## State Hooks
- **`useState`**: Declare a state variable.
    - **Anti-pattern**: Using `useState` for data that can be derived from other state or props (use calculation during render instead).
    - **Future-Proof**: Use the functional initializer `useState(() => value)` for values that need a **stable identity** (like IDs or subscriptions). Unlike `useMemo`, React guarantees it won't be discarded (see [Future-Proof](./bulletproof-examples.md#9-future-proof)).
    - **Note**: Use the functional updater `setCount(prev => prev + 1)` when the new state depends on the previous one.
- **`useReducer`**: Manage complex state logic with an action/reducer pattern.
    - **When to use**: When state updates are complex (nested objects) or many state transitions depend on each other.

## Context Hooks
- **`useContext`**: Subscribe to a React Context.
    - **Anti-pattern**: Overusing context for "global" state that is only used by a small sub-tree (use component composition instead).

## Ref Hooks
- **`useRef`**: Access a DOM node or store a mutable value that doesn't trigger re-renders.
    - **React 19**: Components now receive `ref` as a standard prop. `forwardRef` is deprecated.
    - **Important**: Do not read or write `ref.current` during rendering. Only do it in Effects or Event Handlers.
- **`useId`**: Generates unique IDs that are stable across the server and client.
    - **Instance-Proof**: Use `useId` for all generated strings (accessibility IDs, labels, etc.) to avoid conflicts when multiple instances of a component are rendered (see [Instance-Proof](./bulletproof-examples.md#3-instance-proof)).

## Performance Hooks
- **`useMemo`**: Cache the result of a calculation.
- **`useCallback`**: Cache a function identity.
    - **Note**: Only use these when you have expensive calculations or when passing functions/objects to `React.memo` components. Don't over-optimize.
- **`useTransition`**: Update state without blocking the UI.
    - **Example**: Searching a list while keeping the input responsive.
- **`useDeferredValue`**: Defer updating a non-critical part of the UI.

## Resource Hooks (React 19+)
- **`use`**: Read the value of a resource like a Promise (API call) or Context.
    - **Unique**: Can be used inside loops or conditional statements.
- **`useActionState`**: Manage state for async actions (loading, data, error).
    - **Signature**: `const [state, dispatch, isPending] = useActionState(actionFn, initialState)`
    - **`isPending`**: Automatically `true` while the action is running — no manual `setLoading` needed.
    - **Standard**: The new way to handle `<form action={...}>`. Replaces the `useState` + manual loading flag pattern entirely.
- **`useOptimistic`**: Show a different state while an async action is in progress.

---
### 🔗 Contexto & Relacionados
- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
- **Pattern**: [State Management](../patterns/state-management.md)
- **Pattern**: [Effects & Synchronization](../patterns/effects-synchronization.md)
