# State Management Patterns

Best practices for structuring and managing data flow.

## 1. Principles of State Structure

- **Group Related State**: If two state variables always update together, merge them into an object.
- **Avoid Redundant State**: If you can calculate a value from props or other state, **calculate it during render**.
- **Avoid Contradictory State**: Instead of `isLoading` and `isError`, use a `status` variable.
- **Avoid Duplication**: Don't put props into state unless you want to ignore parent updates.

## 2. Lifting State Up

- When multiple components need the same data, move the state to the **closest common ancestor**.
- Pass the state down as props and the update logic as callbacks.

## 3. Preserving and Resetting State

- React preserves state as long as the component stays in its position in the tree.
- **Resetting**: Use the `key` prop to force React to discard a component's state and recreate it.

## 4. Reducer Pattern (`useReducer`)

- Use when state logic is complex or involves multiple sub-values.
- Decouples _what happened_ (actions) from _how the state updates_ (reducer logic).

## 5. Context Pattern


---

### 🔗 Contexto & Relacionados

- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)
- **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)
- **Reference**: [Hooks Reference](../references/hooks.md)
