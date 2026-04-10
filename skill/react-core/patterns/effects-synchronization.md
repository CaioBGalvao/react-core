# Effects and Synchronization

How to manage side effects and keep your components in sync with external systems.

## 1. Effects vs Events

- **Event Handlers**: Triggered by specific user interactions (click, submit). Logic here should be "imperative".
- **Effects**: Triggered by rendering itself. They "synchronize" the component with something outside of React (network, browser API, non-React widget).

## 2. The Golden Rule: "You Might Not Need an Effect"

- **Don't use Effects to transform data for rendering**: Do it in the function body.
- **Don't use Effects to handle user events**: Use the event handler.
- **Don't use Effects to reset state**: Use a `key`.

## 3. Managing Dependencies

- Every reactive value (props, state, vars derived from them) used in an Effect **must** be in the dependency array.
- If the dependency array is empty `[]`, the effect runs only on mount and cleanup on unmount.
- **Removing dependencies**: Don't suppress lint rules. Instead:
    - Move logic out of the component.
    - Use `useCallback`/`useMemo` to stabilize objects.
    - Use **`useEffectEvent`** (19.2+) to read latest props/state without triggering the effect.
        - **Rule**: Functions returned by `useEffectEvent` **MUST NOT** be included in any dependency array.
        - **Rule**: They **CANNOT** be called during rendering.
        - **Rule**: They **MUST ONLY** be called from inside Effects or other Effect Events (not in regular handlers or passed as props).

## 4. Lifecycle & Cleanups

- Effects have a different lifecycle: they **start** (mount/update) and **stop** (next update/unmount).
- Always return a cleanup function to:
    - Abort fetch requests.
    - Clear timers/intervals.
    - Unsubscribe from websockets/events.
    - Reset non-React UI states.

## 5. Fetching in Effects

- **Race conditions**: If you fetch in an Effect, a later render might finish before an earlier one. Use a `boolean` flag or `AbortController` in the cleanup to ignore stale responses.

---

### 🔗 Contexto & Relacionados

- **Mindset**: [The Rule of Least Power](../mindset/rule-of-least-power.md)
- **Mindset**: [Rules of React](../mindset/rules-of-react.md)
- **Reference**: [Hooks Reference](../references/hooks.md)
