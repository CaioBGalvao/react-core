# Built-in Components Reference

Standard components provided by React for orchestration and optimization.

## `<Fragment>` (`<>`)
- Group multiple elements without adding an extra node to the DOM.
- Use `<Fragment key={...}>` if you need to pass a key during a map.

## `<Suspense>`
- Display a fallback UI while children are loading (e.g., fetching data or lazy loading components).
- **Props**: `fallback` (JSX).

## `<Profiler>`
- Measure the rendering performance of a React tree programmatically.
- **Props**: `id`, `onRender` (callback).

## `<StrictMode>`
- Enables extra development-only checks:
  - Components re-render an extra time to find bugs.
  - Effects run an extra setup/cleanup cycle.
  - Checks for deprecated APIs.

## `<Portal>` (`createPortal`)
- Render children into a different part of the DOM tree (e.g., outside a parent with `overflow: hidden`).
- **Signature**: `createPortal(children, domNode)`.
- **Note**: Events still bubble up the React tree, not the DOM tree.

## `<Activity>` (React 19.2+)
- **Purpose**: Mark a component tree as active or inactive (OFFSCREEN). Inactive trees have their Effects cleaned up and are not rendered, but keep their state.
- **Props**: `mode` ("visible" or "hidden").
- **Use Case**: Tabbed interfaces or modals where you want to preserve state without keeping expensive Effects running.
- **Activity-Proof**: Always use `useLayoutEffect` to disable DOM side effects (like global styles) when `mode="hidden"`.
  ```tsx
  useLayoutEffect(() => {
    if (ref.current) ref.current.media = 'all'; // Enable
    return () => { if (ref.current) ref.current.media = 'not all'; } // Disable
  }, []);
  ```

## `<ViewTransition>` (React 19.2+)
- **Purpose**: Easy implementation of Web View Transitions API for smooth transitions between DOM states.
- **Props**: `name` (string).
- **Use Case**: Hero animations or smooth route transitions.
- **Transition-Proof**: State updates that trigger `<ViewTransition>` animations **must** be wrapped in `startTransition()` to enable the browser API.
  ```tsx
  startTransition(() => { setCurrentPage(nextPage); });
  ```

## Meta Tags (React 19+)
- Direct support for rendering `<title>`, `<meta>`, and `<link>` tags anywhere in the component tree. React will automatically hoist them to the document `<head>`.
