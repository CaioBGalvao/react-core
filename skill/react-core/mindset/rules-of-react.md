# Rules of React (Purity & Stability)

The foundational rules that allow React to optimize your UI and ensure professional stability.

## 1. Components & Hooks Must Be Pure

- **Same Inputs -> Same Output**: Given the same props/state, a component must return the same JSX.
- **No Side Effects during Render**: Never mutate variables outside the component or talk to the browser/network while React is calling your function.
- **Why?**: This allows React to skip re-renders (Memoization), restart rendering if interrupted (Concurrent), and optimize with the React Compiler.

## 2. The React Compiler
- **Automatic Optimization**: In React 19, the compiler automatically memoizes components and values that it proves are pure.
- **Strictness**: The compiler is strict. If you break the rules of purity (like mutating a prop), it will bail out of optimization for that component. Following these rules is no longer just "good practice"—it's a performance requirement.

## 3. React Calls Components and Hooks

- Never call a component like a function: `MyComponent(props)` ❌. Always use JSX: `<MyComponent {...props} />` ✅.
- **Why?**: This gives React control over the component's lifecycle and memory (hooks).

## 4. Rules of Hooks

- **Only Call Hooks at the Top Level**: Not inside loops, conditions, or nested functions.
- **Only Call Hooks from React Functions**: components or custom hooks.
- **Why?**: React relies on the call order of Hooks to associate state with the component.

---

### 🔗 Contexto & Relacionados

- **Pattern**: [Performance Optimization](../patterns/performance-optimization.md)
- **Reference**: [Hooks Reference](../references/hooks.md)
- **Philosophy**: [The UI Runtime](../mindset/ui-as-a-snapshot.md)
