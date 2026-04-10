# Thinking in React

The fundamental process of building apps with React.

## 1. Break the UI into a Component Hierarchy

- Draw boxes around every component and subcomponent in the mockup and give them names.
- Use the **Single Responsibility Principle**: A component should ideally only do one thing. If it grows, it should be decomposed.

## 2. Build a Static Version First

- Render your mockup using props, but **no state**.
- building a static version requires a lot of typing and no thinking, while adding interactivity requires a lot of thinking and not much typing.

## 3. Find the Minimal Representation of UI State

- Ask three questions about each piece of data:
    1. Does it remain unchanged over time? If so, it’s not state.
    2. Is it passed in from a parent via props? If so, it’s not state.
    3. **Can you compute it** based on existing state or props? If so, it is definitely _not_ state!

## 4. Identify Where Your State Should Live

- For each piece of state:
    1. Identify every component that renders something based on that state.
    2. Find their closest common parent.
    3. Decide where the state should live (often the parent or higher).

## 5. Add Inverse Data Flow

- Pass update functions (like `onToggleItem`) down from the parent to child components so they can trigger state changes up the tree.

---

### 🔗 Contexto & Relacionados

- **Mindset**: [UI as a Snapshot](../mindset/ui-as-a-snapshot.md)
- **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)
- **Pattern**: [State Management](../patterns/state-management.md)
