# SSR and Hydration

Understanding the synchronization between server-rendered HTML and client-side React.

## 1. What is Hydration?

Hydration is the process where React, running on the client, "attaches" to the static HTML already rendered by the server. React doesn't recreate the DOM; instead, it tries to adopt the existing DOM by matching it with its internal virtual DOM representation.

## 2. The Flash of Wrong Content (FOUC)

A common issue in SSR is the "hydration flash." This happens when:
1. The server renders an initial state (e.g., `theme: 'light'`).
2. The browser displays this static HTML.
3. React hydrates on the client and detects a different value (e.g., `theme: 'dark'` from `localStorage`).
4. React "fixes" the UI, causing a visible jump or flash.

## 3. Hydration Mismatches

React expects the first client-side render to match the server-side render exactly. Differences in:
- Dates (`new Date()`)
- Random values (`Math.random()`)
- Browser-only APIs (`window`, `localStorage`)
- Conditional rendering based on `window.innerWidth`

...all cause **Hydration Mismatches**, which are slow and can lead to broken UIs.

## 4. The Correct Cycle

To avoid mismatches and flashes:
1. **Server Render**: Render a generic or "skeleton" state that is valid everywhere.
2. **Inline Script (Critical)**: Inject a synchronous `<script>` using `dangerouslySetInnerHTML` to set CSS variables or DOM attributes (like `dataset`) *before* React starts (see [Hydration-Proof](../references/bulletproof-examples.md#2-hydration-proof)). This ensures the visual style is applied before the first paint.
3. **Hydrate**: React attaches to the DOM.
4. **`useEffect`**: Use `useEffect` to read client-side-only values and update the state. Since `useEffect` runs after the UI is committed, it won't crash the server.

## 5. Summary of Best Practices

- **Never call browser APIs during render**: Keep the function body "Server-Proof".
- **Use `useId()`**: Guaranteed to produce the same ID on server and client.
- **Client-only components**: Wrap components that heavily rely on browser APIs in a "Client Only" wrapper that only renders after mount.

---

### 🔗 Contexto & Relacionados

- **Pattern**: [Bulletproof Patterns](../patterns/bulletproof.md)
- **Mindset**: [Composition Architecture](../mindset/composition-architecture.md)
- **Reference**: [DOM APIs](../references/dom-apis.md)
