# Composition Architecture

Building resilient applications by composing small, focused pieces.

## 1. Composition over Inheritance

- React components are building blocks. Instead of extending classes, you nest components and pass data via props.
- **Child Empowerment**: Use the `children` prop to allow a component to wrap any arbitrary content without knowing its structure.

## 2. Lifting State Up

- State should live in the **closest common ancestor** that needs it.
- This creates a "single source of truth" and makes the data flow predictable.

## 3. The Component Tree as a Map

- Visualize your UI as a tree.
- **Top-down Data Flow**: Data flows down (props); events flow up (callbacks).
- **Separation of Concerns**: Divide components into "Containers" (logic/data) and "Presentational" (UI/layout) when complexity grows.

## 4. Slot Pattern & Prop Drilling

- If you are passing props through many layers only to reach a distant child, consider:
  1. **Slots**: Pass components as props.
  2. **Context**: Use for data needed by a deep sub-tree.

### Composition-Proof
- **Avoid `React.cloneElement()`**: It breaks with Server Components, `React.lazy`, and `React.memo` because it requires a "live" React element.
- **Bulletproof Alternative**: Use **Context**. It decouples the parent from children types and works across all component boundaries (see [Composition-Proof](../references/bulletproof-examples.md#5-composition-proof)).


---

### 🔗 Contexto & Relacionados

- **Pattern**: [Thinking in React](../patterns/thinking-in-react.md)
- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)
- **Reference**: [Components Reference](../references/components.md)
