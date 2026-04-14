# Bulletproof Component Patterns

This document consolidates references for bulletproof component patterns.

## From: bulletproof.md

### 1. Patterns Checklist

Eleven patterns that ensure React components survive real-world conditions beyond the happy path — SSR, hydration, concurrent rendering, portals, compiler optimizations, and security.

#### Quick Rules

| # | Pattern | Rule |
| :--- | :--- | :--- |
| 1 | **Server-Proof** | Never call browser APIs (`localStorage`, `window`, `document`) during render. Use `useEffect`. |
| 2 | **Hydration-Proof** | Inject a synchronous inline `<script>` using `dangerouslySetInnerHTML` to set DOM attributes (e.g., `dataset`) before hydration. |
| 3 | **Instance-Proof** | Use `useId()` for all generated IDs. Never hardcode IDs in reusable components. |
| 4 | **Concurrent-Proof** | Deduplicate data fetches to avoid redundant network/DB calls in concurrent renders. |
| 5 | **Composition-Proof** | Use Context instead of `React.cloneElement()` — cloneElement breaks with Server Components, lazy, and memo. |
| 6 | **Portal-Proof** | Use `ref.current?.ownerDocument.defaultView \|\| window` for event listeners, not the global `window`. |
| 7 | **Transition-Proof** | Wrap state updates in `startTransition()` to enable View Transition API animations. |
| 8 | **Activity-Proof** | Use `useLayoutEffect` to disable DOM side effects (e.g., `<style>` tags) when hidden by `<Activity>`. |
| 9 | **Future-Proof** | Use `useState(() => value)` for stable identity. `useMemo` is only a performance hint — React may discard it. |
| 10 | **Compiler-Proof** | Never mutate props, state, or hooks output during render. Keep components pure so the **React Compiler** can optimize them. |
| 11 | **Security-Proof** | Use Taint APIs (`experimental_taintUniqueValue`) in Server Components to ensure sensitive data is never serialized to the client. |

#### Checklist

When building a reusable React component, verify:

- [ ] No browser APIs called during render (server-proof)
- [ ] No hydration flash for client-storage values (hydration-proof)
- [ ] No hardcoded IDs; uses `useId()` (instance-proof)
- [ ] Data fetches deduplicated for concurrent stability (concurrent-proof)
- [ ] Uses Context instead of `cloneElement` (composition-proof)
- [ ] Event listeners use `ownerDocument.defaultView` (portal-proof)
- [ ] State updates wrapped in `startTransition()` where needed (transition-proof)
- [ ] DOM side effects respect `<Activity>` visibility (activity-proof)
- [ ] Stable values use `useState` initializer, not `useMemo` (future-proof)
- [ ] Inputs (props/state) are not mutated during render (compiler-proof)
- [ ] Sensitive data is tainted in Server Components (security-proof)

#### Present Results to User

When reviewing a component against these patterns, format as:

```markdown
**Bulletproof Check: `<ComponentName>`**

| Pattern | Status | Notes |
| :--- | :--- | :--- |
| Server-Proof | PASS/FAIL | ... |
| ... | ... | ... |

**Suggested fixes:**
1. ...
```
##### 🔗 Contexto & Relacionados

- **Reference**: [Bulletproof Examples](./bulletproof-examples.md)
- **Mindset**: [SSR and Hydration](./performance-and-rendering.md)
- **Mindset**: [Composition Architecture](./architecture-and-mindset.md)
