# React Compiler

The React Compiler is a build-time tool that automatically optimizes your React code. It eliminates the need for manual memoization hooks like `useMemo` and `useCallback`, ensuring that your application is as efficient as possible without manual effort.

## 🧠 The Shift: Automatic Memoization

Traditionally, React developers had to manually decide what to memoize to prevent unnecessary re-renders. The Compiler shifts this responsibility to the build step. 

- **Before**: Careful management of dependency arrays in `useMemo`, `useCallback`, and `React.memo`.
- **After**: Write standard React code; the Compiler handles "zero-config" memoization of values and functions.

## 🛠️ Directives (Escape Hatches)

The Compiler provides directives for fine-grained control over which functions should be optimized.

### `"use memo"`
- **Purpose**: Explicitly opts a function (component or hook) into compilation.
- **Usage**: Useful in "annotation mode" where the compiler doesn't infer every component, or to force optimization on a specific piece of code.
```tsx
function StableComponent() {
  "use memo";
  // This component will be optimized by the compiler
  return <div>...</div>;
}
```

### `"use no memo"`
- **Purpose**: Opts a function out of compilation.
- **Usage**: Used as a temporary escape hatch for debugging issues, working with incompatible third-party libraries, or handling edge cases where the compiler's inference causes issues.
```tsx
function ProblematicComponent() {
  "use no memo";
  // TODO: Fix dependency issue before removing this directive
  return <div>...</div>;
}
```
> [!IMPORTANT]
> Use directives sparingly. Always document **why** a function is opted out with `"use no memo"` and create a plan to fix the underlying issue.

## 📏 Rules for Optimization (Bailouts)

The Compiler expects code to follow the [Rules of React](../mindset/rules-of-react.md). If these rules are violated, the compiler may "bail out" (skip) optimization for that component to ensure correctness.

Common reasons for bails outs:
- **Impure Rendering**: Mutating props, state, or global variables during the render phase.
- **Non-standard React**: Using browser APIs (`window`, `localStorage`) directly in the render body instead of `useEffect`.
- **Calling Hooks Conditionally**: Violating the top-level hook rule.

## ⚠️ Performance Nuance

The React Compiler is a massive improvement for code maintainability, but it has important limitations:

-   **Unnecessary vs. Expensive**: The compiler eliminates *unnecessary* re-renders (skipping components whose inputs haven't changed). It does **not** solve *expensive* renders. If a single render of a component is slow (e.g., heavy calculations, complex DOM structures), the compiler will not make that individual render faster.
-   **Composition first**: Strategic use of [Composition Architecture](../mindset/composition-architecture.md) remains a more powerful and predictable performance tool than relying solely on the compiler to find optimizations in a deeply coupled component tree.

## 🏗️ Gradual Adoption

In large codebases, you can configure the compiler in **Annotation Mode** (`compilationMode: 'annotation'`). In this mode, only functions with the `"use memo"` directive will be optimized, allowing you to roll out the compiler one component at a time.

---
### 🔗 Related
- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
- **Reference**: [Hooks Reference](./hooks.md)
