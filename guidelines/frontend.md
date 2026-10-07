---
order: 3
verified: 2026-10-07
---

# Frontend

React with TypeScript, built with Vite. The backend serves the built files.

## Structure

- One file per screen, named after it (`Settings.tsx`). Small shared components go next to the screen that uses them first.
- Function components and hooks only. A screen gets its data through props or its own `useEffect`.
- All server calls go through one module, which holds the response types and adds the base path.
- Model server responses as TypeScript types, with union types for states that differ (`configured: true | false`).
- Keep dependencies few. No UI framework, no state library.

## Paths

- Never hard-code `/`. Build every URL from the base path the server provides, so the app runs under any path.

## Styles

- One stylesheet. Its tokens mirror [`tokens.css`](../design/tokens.css) and the [style guide](../design/style-guide.md).
- Use the tokens, never raw colors or sizes.
- Class names in plain words for what an element is: `status-line`, `button secondary`.
- Build every screen from the [UI kit](../design/ui-kit.md). A new component goes in the UI kit first.

## Behavior

- Use real elements: `<a>` to go somewhere, `<button>` to do something. Icon-only buttons have an `aria-label`, decorative icons `aria-hidden`.
- On a 401 or 403, reload, so the sign-in screen shows. Any other failure shows a message that says what happened and what to do.
- Copy follows the [voice](../design/brand.md#voice): sentence case, buttons that say what happens, no apologies.

## Checks

- `npm run build` type-checks and builds. It must pass before a pull request merges.
