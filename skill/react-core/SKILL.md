---
name: react-core
description: >
  Use this skill for any React task: component design, state logic, hooks 
  usage, performance, Effects, and React 19 APIs (Actions, use, RSC). 
  Activate when writing or reviewing React components, diagnosing re-render 
  issues, deciding where to place state, using useEffect, useRef, useMemo, 
  useCallback, or useTransition, and when building with Inertia + React in 
  this project. Also use when the user asks how to handle async state, 
  optimize list rendering, compose components, or avoid common anti-patterns 
  like unnecessary effects or stale closures.
license: MIT
metadata:
    version: '3.0'
    source: 'https://react.dev'
---

# React Core

## The Mental Model

React is built on several core pillars. Understanding them is key to writing clean, performant code.

- **Thinking in React**: Decomposing UI, identifying state, and managing data flow. See [Thinking in React](./patterns/thinking-in-react.md).
- **Component Purity**: Same inputs -> Same JSX. No side effects during rendering. See [Rules of React](./mindset/rules-of-react.md).
- **Render as a Snapshot**: State updates trigger new renders where props and state are constant. See [UI as a Snapshot](./mindset/ui-as-a-snapshot.md).

For detailed guidance, explore the following categories:

### 🧠 Core Mindsets

- [Rule of Least Power](./mindset/rule-of-least-power.md) - Choosing the simplest tool for the job.
- [Composition Architecture](./mindset/composition-architecture.md) - Building resilient apps by composing small pieces.

### 🏛️ Architectural Patterns

- [State Management](./patterns/state-management.md) - Structure, Lifting State, Reducers, Context.
- [Effects & Synchronization](./patterns/effects-synchronization.md) - Side effects, Event vs Effects, Cleanups.
- [Performance Optimization](./patterns/performance-optimization.md) - Transitions, Memoization, List optimization.
- [Custom Hooks](./patterns/custom-hooks.md) - Extracting logic into reusable functions.
- [Immutability](./patterns/immutability.md) - Updating state without direct mutation.

### 🛡️ Bulletproof Patterns

- [Bulletproof Patterns](./patterns/bulletproof.md) - Nine essential patterns for production-ready components.
- [SSR and Hydration](./mindset/ssr-hydration.md) - Mindset for handling Server-Client synchronization.

### 📚 Technical References

- [Hooks Reference](./references/hooks.md) - All core and advanced hooks.
- [Built-in Components](./references/components.md) - Suspense, Profiler, Portals.
- [DOM & Resource APIs](./references/dom-apis.md) - Portals, preloading, flushSync.
- [React 19 Updates](./references/react-19.md) - Actions, `use`, RSC.

### ⚖️ Decision Frameworks

When faced with architectural choices, evaluate them using these mindsets:

- **Prop Drilling?** Use [Composition Architecture](./mindset/composition-architecture.md) (Slots/Children) first. Only use Context if the data is truly app-wide.
- **New State?** Apply the [Rule of Least Power](./mindset/rule-of-least-power.md). Can it be derived during render? If the state update logic is complex, use a Reducer.
- **Syncing with External Systems?** Use [Effects & Synchronization](./patterns/effects-synchronization.md) purely as an **escape hatch**, not for data flow within React.

## ⚠️ Gotchas

- **Derived State as `useEffect`**: Updating state because a prop changed is an anti-pattern. Calculate the "derived" value directly in the function body during render.
- **`useEffect` with `async`**: You cannot make the Effect callback `async`. Use an internal function: `useEffect(() => { const load = async () => {...}; load(); }, [])`.
- **Arrays/Objects in Dependencies**: Including fresh objects/arrays in the dependency array causes infinite loops. Stabilize them with `useMemo` or move them outside.
- **`useRef` vs `useState`**: `useRef` updates do not trigger re-renders. Only use it for values that don't affect the UI.
- **Stable Keys**: Never use array index as `key` for dynamic lists. It destroys component state and performance.
- **Stale Closures & `useEffectEvent`**: Effects/callbacks might capture old values. Use functional updaters or **`useEffectEvent`** (19.2+) to read the latest values without triggering re-runs. 
    - **CRITICAL**: Functions returned by `useEffectEvent` **MUST NOT** be included in any dependency array. 
    - **CRITICAL**: They **CANNOT** be called during rendering.
    - **CRITICAL**: They **MUST ONLY** be called from inside Effects (or other Effect Events). Do not call them in regular event handlers or pass them down as props to components/hooks.
- **Async Forms (React 19 Actions)**: For async form submissions with loading state, use **`useActionState`** instead of manual `useState` + `useEffect`. It returns `[state, dispatch, isPending]` — React manages `isPending` automatically during the async transition. No manual `setLoading(true/false)` needed.
- **Client Component Overkill**: Adding `'use client'` to a high-level wrapper just to add a small toggle or state can be an anti-pattern in RSC environments. It may turn larger trees than necessary into client-rendered branches. Always extract interactive logic into small "leaf" components (e.g., `Header`, `Button`, `SearchBox`) and keep parents as Server Components where possible.
- **Server API Crash**: Calling browser APIs like `window` or `localStorage` during render will crash Server Components or during SSR. Always use `useEffect` for browser-only logic.
- **`cloneElement` Fragility**: `React.cloneElement()` often breaks with Server Components, `React.lazy`, or `React.memo`. Prefer [Composition Architecture](./mindset/composition-architecture.md) with Context.

## Core Rules

1. **Apply the Rule of Least Power**: Choose the simplest tool (CSS > JSX > Props > Context > Effects).
2. **Don't use Effects if you can compute during render.**
3. **Treat State as Immutable** (never mutate objects/arrays directly).
4. **Keep components focused and small** (Single Responsibility Principle).
5. **Use stable keys for lists.**
6. **Only call hooks at the top level.**
7. **Minimize Client Component Boundaries**: Move interactivity (state/hooks) to small, specific child components. This keeps the parent tree as Server Components, improving performance in RSC-ready environments.
8. **Follow the Bulletproof Checklist**: Ensure components survive SSR, hydration, and concurrent rendering.
