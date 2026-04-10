# Reusing Logic with Custom Hooks

Extract repetitive logic into reusable hooks to keep components clean and focused.

## 1. When to Create a Custom Hook

- **Stateful Logic Reuse**: Whenever two or more components share the same stateful logic (complex state, effects, subscriptions).
- **Component Simplification**: A component is doing too many things; extract the "data fetching" or "form handling" part.
- **Abstraction**: Hide the complexity of browser APIs or third-party libraries.

## 2. Naming Convention

- **Must start with `use`**: This tells React and linters that it follows the Rules of Hooks.
- **Descriptive**: `useOnlineStatus`, `useChatRoom`, `useFormInput`.

## 3. Composition Patterns

- **Sharing State vs Logic**: Custom hooks share *logic*, not *state*. Each call to a hook gets its own independent state.
- **Passing Props**: You can pass reactive values (props, state) to custom hooks. They will re-run when the values change.
- **Returning Values**: Return what the component needs: values, functions, or objects. Arrays are good for small sets (like `useState`), objects are better for many values.

## 4. Best Practices

- **Only Hooks inside**: Don't put non-hook logic (like API calls directly) inside a hook if it doesn't need to be there; keep them pure.
- **Avoid deep nesting**: Try not to have hooks calling hooks calling hooks (too many layers of abstraction).
- **Custom Hook as a Black Box**: The component using it shouldn't care *how* it works, only what goes in and what comes out.

---

### 🔗 Contexto & Relacionados

- **Patterns**: [State Management Patterns](../patterns/state-management.md)
- **Mindset**: [The Rule of Least Power](../mindset/rule-of-least-power.md)
