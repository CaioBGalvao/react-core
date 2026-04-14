# Architecture and Mindset

This document consolidates references for architecture and mindset.

## From: composition-architecture.md

### Composition Architecture

Building resilient applications by composing small, focused pieces.

#### 0. Elements vs Components (Mental Model)

Understanding this distinction is the key to advanced React patterns and performance:

- **Component**: The function or class you define (the "blueprint").
- **Element**: The plain JavaScript object returned by calling the component (the "result").
  - *Logic:* When you use `<MyComponent />` in JSX, you are creating an **Element**.
  - *Key Insight:* Elements are immutable. If an element is passed from outside a re-rendering component as a prop (like `children`), React can detect that the element's object identity hasn't changed and **skips the entire rendering process** for that subtree. This is why composition is often better than memoization.

#### 1. Composition over Inheritance

- React components are building blocks. Instead of extending classes, you nest components and pass data via props.
- **Child Empowerment**: Use the `children` prop to allow a component to wrap any arbitrary content without knowing its structure.

#### 2. Lifting State Up

- State should live in the **closest common ancestor** that needs it.
- This creates a "single source of truth" and makes the data flow predictable.

#### 3. The Component Tree as a Map

- Visualize your UI as a tree.
- **Top-down Data Flow**: Data flows down (props); events flow up (callbacks).
- **Separation of Concerns**: Divide components into "Containers" (logic/data) and "Presentational" (UI/layout) when complexity grows.

#### 4. Slot Pattern & Prop Drilling

- If you are passing props through many layers only to reach a distant child, consider:
  1. **Slots**: Pass components as props.
  2. **Context**: Use for data needed by a deep sub-tree.

##### Composition-Proof

- **Avoid `React.cloneElement()`**: It breaks with Server Components, `React.lazy`, and `React.memo` because it requires a "live" React element.
- **Bulletproof Alternative**: Use **Context**. It decouples the parent from children types and works across all component boundaries (see [Composition-Proof](./bulletproof-examples.md#5-composition-proof)).

#### 5. Composition Across Boundaries (Fractal Islands)

A major advantage of the React composition model is that it is **fractal**. You can nest components from different environments:

1. **The Slot Pattern**: A Client Component (with `'use client'`) can accept any JSX as `children` or other "slots" (props).
2. **Server Components as Slots**: You can pass a **Server Component** (which has direct access to data) into a slot of a **Client Component**.
3. **Result**: The Client Component manages the interactive "shell," while the Server Component handles the data-heavy content inside it. This avoids "prop drilling" serialized data across the boundary.

> [!IMPORTANT]
> This works because the Server Component is rendered *before* the Client Component. The Client Component just receives the result (the "Element") and doesn't need to know how it was rendered.

##### 🔗 Architecture: Contexto & Relacionados

- **Pattern**: [Thinking in React](./architecture-and-mindset.md)
- **Mindset**: [UI as a Snapshot](./architecture-and-mindset.md)
- **Reference**: [Components Reference](./components.md)

## From: rules-of-react.md

### Rules of React (Purity & Stability)

The foundational rules that allow React to optimize your UI and ensure professional stability.

#### 1. Components & Hooks Must Be Pure

- **Same Inputs -> Same Output**: Given the same props/state, a component must return the same JSX.
- **Side effects must not run in render**: Never talk to the browser APIs, network, or external systems during render. Use `useEffect`.
- **Do not mutate inputs**: React assumes that props, state, and context are immutable. Never mutate them.
- **Local Mutation is fine**: Mutating a variable created *inside* the component during render is safe and encouraged for performance, as long as it doesn't leak out.

#### 2. The React Compiler & Purity

- **Automatic Optimization**: In React 19, the compiler automatically memoizes components and values that it proves are pure.
- **Strictness**: The compiler is strict. If you break the rules of purity (like mutating a prop or a value returned from a hook), it will **bail out** of optimization for that component. Following these rules is no longer just "good practice"—it's a performance requirement for modern React apps.

#### 3. React Calls Components and Hooks

- Never call a component like a function: `MyComponent(props)` ❌. Always use JSX: `<MyComponent {...props} />` ✅.
- **Why?**: This gives React control over the component's lifecycle and memory (hooks).

#### 4. Rules of Hooks

- **Only Call Hooks at the Top Level**: Not inside loops, conditions, or nested functions.
- **Only Call Hooks from React Functions**: components or custom hooks.
- **Why?**: React relies on the call order of Hooks to associate state with the component.

##### 🔗 Rules: Contexto & Relacionados

- **Pattern**: [Performance Optimization](./performance-and-rendering.md)
- **Reference**: [Hooks Reference](./hooks.md)
- **Philosophy**: [The UI Runtime](./architecture-and-mindset.md)

## From: ui-as-a-snapshot.md

### UI as a Snapshot

Understanding how React synchronizes the UI with your state.

#### 1. Declarative UI

- You don't tell React "how" to update the DOM. You tell React "what" the UI looks like for a given state.
- **Data (State/Props) -> UI (JSX)**: The UI is a pure function of your data.

#### 2. Rendering is a Snapshot

- When React calls your component, it takes a "snapshot" of the state and props at that moment.
- **State doesn't change during render**: If you have `const [count, setCount] = useState(0)` and call `setCount(count + 1)`, the variable `count` is still `0` in the current function execution.

#### 3. The React Lifecycle: Trigger -> Render -> Commit

- **Trigger**: A state change or initial mount.
- **Render**: React calls your component to figure out what should change.
- **Commit**: React updates the DOM to match the result of the render.

#### 4. Reactivity is Automatic

- You don't need to manually update elements. When the state changes, React automatically re-triggers the snapshot cycle.

##### 🔗 Snapshot: Contexto & Relacionados

- **Pattern**: [Thinking in React](./architecture-and-mindset.md)
- **Pattern**: [State Management](./component-patterns.md)
- **Mindset**: [Composition Architecture](./architecture-and-mindset.md)

## From: thinking-in-react.md

### Thinking in React

The fundamental process of building apps with React.

#### 1. Break the UI into a Component Hierarchy

- Draw boxes around every component and subcomponent in the mockup and give them names.
- Use the **Single Responsibility Principle**: A component should ideally only do one thing. If it grows, it should be decomposed.

#### 2. Build a Static Version First

- Render your mockup using props, but **no state**.
- building a static version requires a lot of typing and no thinking, while adding interactivity requires a lot of thinking and not much typing.

#### 3. Find the Minimal Representation of UI State

- Ask three questions about each piece of data:
    1. Does it remain unchanged over time? If so, it’s not state.
    2. Is it passed in from a parent via props? If so, it’s not state.
    3. **Can you compute it** based on existing state or props? If so, it is definitely *not* state!

#### 4. Identify Where Your State Should Live

- For each piece of state:
    1. Identify every component that renders something based on that state.
    2. Find their closest common parent.
    3. Decide where the state should live (often the parent or higher).

#### 5. Add Inverse Data Flow

- Pass update functions (like `onToggleItem`) down from the parent to child components so they can trigger state changes up the tree.

##### 🔗 Thinking: Contexto & Relacionados

- **Mindset**: [UI as a Snapshot](./architecture-and-mindset.md)
- **Mindset**: [Composition Architecture](./architecture-and-mindset.md)
- **Pattern**: [State Management](./component-patterns.md)
