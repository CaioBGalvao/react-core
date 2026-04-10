# UI as a Snapshot

Understanding how React synchronizes the UI with your state.

## 1. Declarative UI

- You don't tell React "how" to update the DOM. You tell React "what" the UI looks like for a given state.
- **Data (State/Props) -> UI (JSX)**: The UI is a pure function of your data.

## 2. Rendering is a Snapshot

- When React calls your component, it takes a "snapshot" of the state and props at that moment.
- **State doesn't change during render**: If you have `const [count, setCount] = useState(0)` and call `setCount(count + 1)`, the variable `count` is still `0` in the current function execution.

## 3. The React Lifecycle: Trigger -> Render -> Commit

- **Trigger**: A state change or initial mount.
- **Render**: React calls your component to figure out what should change.
- **Commit**: React updates the DOM to match the result of the render.

## 4. Reactivity is Automatic

- You don't need to manually update elements. When the state changes, React automatically re-triggers the snapshot cycle.

---

### 🔗 Contexto & Relacionados

- **Pattern**: [Thinking in React](../patterns/thinking-in-react.md)
- **Pattern**: [State Management](../patterns/state-management.md)
- **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)
