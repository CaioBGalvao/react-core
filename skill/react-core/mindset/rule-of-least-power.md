# The Rule of Least Power

Choosing the simplest, most predictable tool for the job. Prevents over-engineering and bugs.

## 1. The Hierarchy of Power

When solving a problem, choose the lowest level of "power" that works:

1.  **CSS**: For layouts, basic transitions, and hover states. (Least power, most predictable).
2.  **Pure JSX**: For static structure and simple conditional rendering.
3.  **Props**: For passing data from parents to children.
4.  **State**: For data that changes over time and affects the UI.
5.  **Context**: For sharing data across many components (Power grows, predictability shrinks).
6.  **Effects & Refs**: For talking to the world outside React. (Highest power, "Escape Hatches").

## 2. Why Choose Least Power?

- **Predictability**: CSS and Pure JSX are easier to reason about than complex Effect logic.
- **Performance**: Pure rendering is faster than synchronizing with side effects.
- **Maintainability**: Low-power code has fewer edge cases and is easier to test.

## 3. Practical Application

- Don't use `useEffect` to format a string if you can do it in the function body.

---

### 🔗 Contexto & Relacionados

- **Pattern**: [Effects & Synchronization](../patterns/effects-synchronization.md)
- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
- **Reference**: [DOM APIs](../references/dom-apis.md)
