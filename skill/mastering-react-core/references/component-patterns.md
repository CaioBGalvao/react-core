# Component Patterns

This document consolidates references for component patterns.

## From: state-management.md

### State Management Patterns

Best practices for structuring and managing data flow.

#### 0. Categories of State

Before deciding *how* to manage state, identify *what category* it belongs to:

1.  **Local State**: Data used by one or a few components (e.g., a toggle). Handle with `useState`.
2.  **Shared State**: Data used by multiple distant components (e.g., user profile). Handle with **Composition** first, then **Context**.
3.  **Remote State**: Data from a server (e.g., API response). Handle with **React 19 Actions** (`useActionState`) or the `use` API. Avoid manual `useEffect` fetching if possible.
4.  **URL State**: Data stored in the address bar (e.g., filters, search). Use **URL params** as the source of truth to ensure bookmarking and "Back" button support.

#### 1. Principles of State Structure

- **Group Related State**: If two state variables always update together, merge them into an object.
- **Avoid Redundant State**: If you can calculate a value from props or other state, **calculate it during render**.
- **Avoid Contradictory State**: Instead of `isLoading` and `isError`, use a `status` variable.
- **Avoid Duplication (Single Source of Truth)**: Never mirror props or other state into a new `useState`. This creates a "Source of Truth" bug where the local state becomes out of sync with the parent. 
    - *Exception:* When the prop is only used as an **initial seed** and the component intentionally forks from the parent (e.g., a "Reset" form). In this case, use the name `initialValue` for the prop to clarify intent.
- **Lazy Initializer**: For expensive one-time initializations, pass a function to `useState`: `useState(() => createComplexObject())`. This prevents the expensive logic from running on every re-render.

#### 2. Lifting State Up

- When multiple components need the same data, move the state to the **closest common ancestor**.
- Pass the state down as props and the update logic as callbacks.

#### 3. Preserving and Resetting State

- React preserves state as long as the component stays in its position in the tree.
- **Resetting**: Use the `key` prop to force React to discard a component's state and recreate it.

#### 4. Reducer Pattern (`useReducer`)

- Use when state logic is complex or involves multiple sub-values.
- Decouples _what happened_ (actions) from _how the state updates_ (reducer logic).

#### 5. Context Pattern



##### 🔗 Contexto & Relacionados

- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)
- **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)
- **Reference**: [Hooks Reference](../references/hooks.md)


## From: effects-synchronization.md

### Effects and Synchronization

How to manage side effects and keep your components in sync with external systems.

#### 1. Effects vs Events

- **Event Handlers**: Triggered by specific user interactions (click, submit). Logic here should be "imperative".
- **Effects**: Triggered by rendering itself. They "synchronize" the component with something outside of React (network, browser API, non-React widget).

#### 2. The Golden Rule: "You Might Not Need an Effect"

- **Don't use Effects to transform data for rendering**: Do it in the function body.
- **Don't use Effects to handle user events**: Use the event handler.
- **Don't use Effects to reset state**: Use a `key`.

#### 3. Stale Closures (The Hook Pitfall)

Effects (and other hooks) are "closures" that capture props and state at the moment they were defined (during render).

- **The Problem**: If you don't include a reactive value in the dependency array, the Effect will continue using the "old" value from a previous render.
- **The Solution**: Always use the ESLint plugin for React Hooks. If you find yourself wanting to "skip" a dependency, it usually means your logic should be moved to an event handler or you should use `useEffectEvent`.

#### 4. Lifecycle & Cleanups

- Effects have a different lifecycle: they **start** (mount/update) and **stop** (next update/unmount).
- Always return a cleanup function to:
    - Abort fetch requests.
    - Clear timers/intervals.
    - Unsubscribe from websockets/events.
    - Reset non-React UI states.

#### 5. Fetching in Effects

- **Race conditions**: If you fetch in an Effect, a later render might finish before an earlier one. Use a `boolean` flag or `AbortController` in the cleanup to ignore stale responses.

#### 6. Synchronizing with External Stores (`useSyncExternalStore`)

- **Purpose**: Subscribes to an external data source and reads a "snapshot" of its state.
- **Benefit**: Prevents "tearing" (UI showing different values for the same state in the same render) and is more performant than manual `useEffect` + `useState`.
- **SSR/Hydration**: If you are using Server-Side Rendering (SSR), `useSyncExternalStore` accepts a third argument: `getServerSnapshot`. This ensures the server-rendered HTML matches the initial client snapshot, preventing "Hydration Mismatches".

##### Example: Browser Online Status
```tsx
import { useSyncExternalStore } from 'react';

function getSnapshot() {
  return navigator.onLine;
}

function subscribe(callback) {
  window.addEventListener('online', callback);
  window.addEventListener('offline', callback);
  return () => {
    window.removeEventListener('online', callback);
    window.removeEventListener('offline', callback);
  };
}

export function ChatIndicator() {
  const isOnline = useSyncExternalStore(subscribe, getSnapshot);
  return <h1>{isOnline ? '✅ Online' : '❌ Disconnected'}</h1>;
}
```


##### 🔗 Contexto & Relacionados

- **Mindset**: [The Rule of Least Power](../mindset/rule-of-least-power.md)
- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
- **Reference**: [Hooks Reference](../references/hooks.md)


## From: refs-and-escape-hatches.md

### Refs and Escape Hatches

How to exit the "React model" to interact with external systems or the DOM directly without triggering re-renders.

#### 0. The Mental Model

While props and state are "declarative" (you describe what the UI should look like), **Refs** are "imperative" (you take action on a specific element). 

Use Refs only when you can't do something declaratively:
- Managing focus, text selection, or media playback.
- Triggering imperative animations.
- Integrating with third-party DOM libraries.
- Storing values that don't affect the UI (identity stability).

#### 1. `useRef` vs `useState`

| `useRef(initialValue)` | `useState(initialValue)` |
| :--- | :--- |
| Returns an object with a `{ current: ... }` property. | Returns a state variable and a setter function. |
| **Doesn't** trigger a re-render when changed. | **Triggers** a re-render when changed. |
| Mutable: you can modify `ref.current` directly. | Immutable: you must use the setter. |
| Read/Write during the render phase? **NO**. | Read/Write during the render phase? **YES**. |

> [!WARNING]
> **Do not read or write `ref.current` during rendering.** 
> React expects the render function to be pure. Reading or writing refs during rendering makes your component's behavior unpredictable. Only access refs inside `useEffect`, `useLayoutEffect`, or event handlers.

#### 2. Callback Refs (Superior to `useEffect`)

A common mistake is using `useEffect` to interact with a DOM element after it mounts.

```javascript
// ❌ Suboptimal: useEffect + useRef
const inputRef = useRef(null);
useEffect(() => {
  if (inputRef.current) inputRef.current.focus();
}, []);
return <input ref={inputRef} />;
```

**Why it's suboptimal:** If the element is rendered conditionally or late, `useEffect` might run before the ref is attached, or you might need complex logic to track when the element actually appears.

**✅ Better: Callback Refs**
React will call your function with the DOM element when it mounts, and with `null` when it unmounts.

```javascript
const inputRef = (node) => {
  if (node !== null) {
    node.focus();
  }
};

return <input ref={inputRef} />;
```

#### 3. Forwarding Refs (`forwardRef` vs React 19)

- **Pre-React 19**: You had to wrap components in `forwardRef` to receive a `ref` prop.
- **React 19+**: `ref` is now a regular prop. You can pass it just like any other prop without `forwardRef`.

#### 4. `useImperativeHandle`

Rarely needed. Use it to customize the instance value that is exposed to parent components when using a ref. Instead of exposing the whole DOM node, you can expose only specific methods (e.g., `.focus()`, `.scrollIntoView()`).


##### 🔗 Contexto & Relacionados

- **Patterns**: [Performance Optimization](./performance-optimization.md)
- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)


## From: compound-components.md

### Compound Components

A powerful pattern for building expressive and flexible component APIs where several components work together to share state and logic implicitly.

#### 1. The Problem: Prop Drilling and Rigid APIs

Consider a `Select` component. A standard API might look like this:

```javascript
// ❌ Rigid API
<Select 
  options={[{label: 'A', value: 'a'}, {label: 'B', value: 'b'}]} 
  onChange={handleChange}
/>
```

This is hard to customize (e.g., adding an icon to one specific option) and leads to "prop bloating" as you add more features.

#### 2. The Solution: Compound Components

Instead of passing data, we pass components. Shared state is managed via a hidden **Context**.

```javascript
// ✅ Flexible API
<Select onChange={handleChange}>
  <Select.Option value="a">Option A</Select.Option>
  <Select.Option value="b">Option B <Icon /></Select.Option>
</Select>
```

#### 3. Implementation Steps

1.  **Create a Context**: Stores the shared state (e.g., which option is selected).
2.  **Parent Component**: Provides the Context and manages the state.
3.  **Child Components**: Consume the Context to display themselves or trigger updates.

##### Basic Example

```javascript
const SelectContext = createContext();

function Select({ children, onChange }) {
  const [selected, setSelected] = useState(null);

  const select = (value) => {
    setSelected(value);
    onChange(value);
  };

  return (
    <SelectContext.Provider value={{ selected, select }}>
      <div className="select-wrapper">{children}</div>
    </SelectContext.Provider>
  );
}

function Option({ value, children }) {
  const { selected, select } = useContext(SelectContext);
  const isSelected = selected === value;

  return (
    <div 
      className={`option ${isSelected ? 'active' : ''}`}
      onClick={() => select(value)}
    >
      {children}
    </div>
  );
}

Select.Option = Option;
```

#### 4. Why Use This?

-   **Implicit State**: The user doesn't have to manually wire up `selected` and `onSelect` for every single option.
-   **Flexibility**: Users can reorder components, wrap them in other divs, or add custom elements anywhere in the tree.
-   **Separation of Concerns**: The parent handles the "how" (logic), while children handle the "what" (display).

#### 5. Type Safety (TypeScript)

When using this pattern with TypeScript, ensure the child components are correctly typed to expect the context. You can also use a custom hook to consume the context and throw an error if a child is used outside its parent:

```typescript
function useSelectContext() {
  const context = useContext(SelectContext);
  if (!context) {
    throw new Error("Select.Option must be used within a <Select>");
  }
  return context;
}
```


##### 🔗 Contexto & Relacionados

-   **Patterns**: [State Management](./state-management.md)
-   **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)


## From: custom-hooks.md

### Reusing Logic with Custom Hooks

Extract repetitive logic into reusable hooks to keep components clean and focused.

#### 1. When to Create a Custom Hook

- **Stateful Logic Reuse**: Whenever two or more components share the same stateful logic (complex state, effects, subscriptions).
- **Component Simplification**: A component is doing too many things; extract the "data fetching" or "form handling" part.
- **Abstraction**: Hide the complexity of browser APIs or third-party libraries.

#### 2. Naming Convention

- **Must start with `use`**: This tells React and linters that it follows the Rules of Hooks.
- **Descriptive**: `useOnlineStatus`, `useChatRoom`, `useFormInput`.

#### 3. Composition Patterns

- **Sharing State vs Logic**: Custom hooks share *logic*, not *state*. Each call to a hook gets its own independent state.
- **Passing Props**: You can pass reactive values (props, state) to custom hooks. They will re-run when the values change.
- **Returning Values**: Return what the component needs: values, functions, or objects. Arrays are good for small sets (like `useState`), objects are better for many values.

#### 4. Best Practices

- **Only Hooks inside**: Don't put non-hook logic (like API calls directly) inside a hook if it doesn't need to be there; keep them pure.
- **Avoid deep nesting**: Try not to have hooks calling hooks calling hooks (too many layers of abstraction).
- **Custom Hook as a Black Box**: The component using it shouldn't care *how* it works, only what goes in and what comes out.

#### 5. Performance Debt Warning

-   **Re-render Cascades**: If a custom hook returns many pieces of state, and one of them changes, **all** components using that hook will re-render, even if they only use a different (unchanged) part of the returned data.
    -   *Rule of thumb:* Keep hooks focused. If a hook manages multiple unrelated stateful objects, split it into smaller hooks or use **Composition** to isolate state from expensive UI components.


##### 🔗 Contexto & Relacionados

- **Patterns**: [State Management Patterns](../patterns/state-management.md)
- **Mindset**: [The Rule of Least Power](../mindset/rule-of-least-power.md)


