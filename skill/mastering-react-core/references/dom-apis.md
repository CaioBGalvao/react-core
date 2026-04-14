# DOM and Resource APIs

APIs for interacting with the DOM and optimizing resource loading.

## DOM APIs (`react-dom`)
- **`createPortal`**: Render JSX into a different DOM node.
- **`flushSync`**: Force React to flush any pending updates synchronously. Use sparingly (e.g., if a browser API requires an immediate DOM change).
- **`findDOMNode`**: **Deprecated**. Use `useRef` and `ref` instead.
- **`hydrateRoot`**: Used to attach React to existing HTML rendered on the server.
- **`createRoot`**: Standard entry point for client-side React apps.

## Resource Loading (React 19+)
Optimize initial load by pre-loading resources as soon as possible.
- **`prefetchDNS(url)`**: DNS lookup for a domain you'll soon connect to.
- **`preconnect(url)`**: Establish a connection to a domain (includes DNS + TLS).
- **`preload(url, options)`**: Start downloading a high-priority resource (image, script, style).
- **`preinit(url, options)`**: Download and execute a script or style.

## Form Actions
- **`requestFormReset`**: Programmatically reset a `<form>` element if using the built-in form handling.

## Server APIs
- **`renderToPipeableStream`**: (Node.js) Renders a React tree to a pipeable Node.js Stream for Server-Side Rendering (SSR) with Suspense support.
- **`renderToReadableStream`**: (Web Streams) Renders for environments like Cloudflare Workers.
