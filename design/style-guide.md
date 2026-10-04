# Style guide

The design tokens behind every Ohara screen. They are also available as CSS custom properties in [`tokens.css`](tokens.css).

## Color

Ohara has one dark theme.

| Token | Value | Use |
|---|---|---|
| `--bg` | `#141413` | Page background |
| `--bg-2` | `#1C1B19` | Panels, code blocks, inputs, the current menu item |
| `--surface-3` | `#2A2926` | Avatars and other small filled shapes |
| `--line` | `#2C2B28` | Borders and dividers, always 1px |
| `--ink` | `#FAF9F5` | Titles and primary text |
| `--text` | `#DCD9D0` | Body text |
| `--muted` | `#A6A39A` | Secondary text, labels, inactive menu items |
| `--clay` | `#D97757` | The accent: buttons, current state, code keywords |
| `--link` | `#E8916F` | Links in body text |
| `--on-clay` | `#141413` | Text and icons on a clay fill |

### Contrast

All text pairs meet WCAG AA:

| Text | Background | Ratio |
|---|---|---|
| `--ink` | `--bg` | 17.5:1 |
| `--text` | `--bg` | 13.1:1 |
| `--muted` | `--bg` | 7.3:1 |
| `--muted` | `--bg-2` | 6.8:1 |
| `--link` | `--bg` | 7.6:1 |
| `--clay` | `--bg` | 5.9:1 |
| `--on-clay` | `--clay` | 5.9:1 |

## Typography

| Style | Font | Size | Weight | Line height | Letter spacing |
|---|---|---|---|---|---|
| Display (sign-in, setup) | Newsreader | 64–76px | 300 | 1.0 | -0.025em |
| H1 | Newsreader | 60px | 300 | 1.02 | -0.02em |
| H2 | Newsreader | 32px | 400 | 1.1 | -0.015em |
| H3 | Manrope | 20px | 600 | 1.3 | -0.01em |
| Lead | Manrope | 19px | 400 | 1.6 | 0 |
| Body | Manrope | 17px | 400 | 1.75 | 0 |
| UI | Manrope | 14–15px | 500–600 | 1.5 | 0 |
| Quote | Newsreader italic | 24px | 300 | 1.4 | 0 |
| Code | JetBrains Mono | 14px | 400 | 1.7 | 0 |
| Label | JetBrains Mono | 12px | 400 | 1.5 | 0 |

- Labels are lowercase monospace, never all caps: `guidelines/`, `on this page`, `copy`.
- Body text is at most 720px wide, about 72 characters per line.
- Turn off ligatures in code (`font-variant-ligatures: none`) so `->` shows as typed.
- On a phone (under 860px), H1 is 44px, H2 is 27px, and body is 16px.

## Spacing

A 4px base. Use these steps: 4, 8, 12, 16, 20, 24, 28, 32, 40, 48, 56, 64, 72, 96.

| Context | Value |
|---|---|
| Space after H1 and lead | 40px |
| Space before a block (code, list, quote) | 20px |
| Space after a block | 40px |
| Page gutter, desktop | 64px |
| Page gutter, phone | 20px |
| Gap between menu items | 0 (8px vertical padding each) |

## Shapes

| Element | Radius |
|---|---|
| Buttons, chips | 999px (pill) |
| Panels, code blocks, inputs, page links | 14px |
| Menu items | 10px |
| Inline code | 6px |
| Dots and avatars | 50% |

- No shadows and no gradients. Hierarchy comes from `--bg-2` surfaces and 1px `--line` borders.
- Clay dots (7–8px) mark state: the current page, a code block's language, a status line.

## Layout

| Region | Width |
|---|---|
| Top bar | full width, 72px tall (64px on a phone) |
| Menu | 280px |
| Content | up to 720px |
| On this page | 220px |
| Max page width | 1440px |

Under 860px, the menu moves behind a button in the top bar and "On this page" is hidden.

## Motion

Motion only answers an action: opening the menu, copying code. Keep it short (150–250ms, ease-out) and respect `prefers-reduced-motion`.

## Accessibility

- Touch targets are at least 44px.
- Every interactive element is a real `<a>`, `<button>`, or `<input>` with a `<label>`.
- Focus is a 2px `--clay` outline with a 2px offset.
- Icon-only buttons have an `aria-label`.

## Copy

- Sentence case everywhere.
- Buttons say exactly what happens: "Sign in with GitHub", "Create GitHub App", "Sign out".
- Errors say what went wrong and how to fix it, with no apology.
- Empty states invite an action: "Create a `README.md` at the root of the repository. It becomes this page once merged."
