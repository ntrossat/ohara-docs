---
verified: 2026-11-25
---


# Style guide

The design tokens behind every Ohara screen. They are also available as CSS custom properties in [`tokens.css`](tokens.css).

## Color

Ohara has one light theme.

| Token | Value | Use |
|---|---|---|
| `--bg` | `#FFFFFF` | Page background |
| `--bg-2` | `#FFFFFF` | Panels, cards, and inputs |
| `--tint` | `#FAFAFA` | Code blocks, inline code, and quiet cards |
| `--soft` | `#F4F4F5` | The current menu item, avatars, and other small filled shapes |
| `--line` | `#E4E4E7` | Borders and dividers, always 1px |
| `--line-soft` | `#ECECEE` | The top bar's edge |
| `--line-strong` | `#D4D4D8` | Borders of inputs and secondary buttons |
| `--ink` | `#18181B` | Titles and primary text |
| `--text` | `#52525B` | Body text and menu items |
| `--muted` | `#63636B` | Secondary text and labels |
| `--faint` | `#8A8A93` | Borders on hover. Never small text |
| `--clay` | `#FF6B1A` | The orange accent: the main button, current state, status dots |
| `--clay-hover` | `#FF8A45` | Primary buttons on hover |
| `--clay-soft` | `#FFE4D4` | Selected text |
| `--link` | `#C2410C` | Links in body text, code keywords, and other small accent text |
| `--on-clay` | `#18181B` | Text and icons on an orange fill |
| `--error` | `#B91C1C` | Error messages |

### Contrast

All text pairs meet WCAG AA:

| Text | Background | Ratio |
|---|---|---|
| `--ink` | `--bg` | 17.7:1 |
| `--text` | `--bg` | 7.7:1 |
| `--text` | `--soft` | 7.0:1 |
| `--muted` | `--bg` | 6.0:1 |
| `--muted` | `--tint` | 5.7:1 |
| `--link` | `--bg` | 5.2:1 |
| `--link` | `--tint` | 5.0:1 |
| `--error` | `--bg` | 6.5:1 |
| `--on-clay` | `--clay` | 6.2:1 |

`--clay` on `--bg` is 2.8:1, so it never carries text: use it for fills, dots, bars, and focus outlines.

## Typography

| Style | Font | Size | Weight | Line height | Letter spacing |
|---|---|---|---|---|---|
| Page headline (setup) | Geist | 40–56px | 600 | 1.04 | -0.045em |
| Display (cards: sign-in, connect) | Geist | 32px | 600 | 1.15 | -0.035em |
| H1 | Geist | 48px | 600 | 1.04 | -0.045em |
| H2 | Geist | 28px | 600 | 1.2 | -0.03em |
| H3 | Geist | 18px | 600 | 1.3 | -0.02em |
| Lead | Geist | 19px | 400 | 1.6 | 0 |
| Body | Geist | 16px | 400 | 1.7 | 0 |
| UI | Geist | 14–15px | 400–600 | 1.5 | 0 |
| Quote | Geist | 19px | 400 | 1.6 | 0 |
| Code | Geist Mono | 14px | 400 | 1.7 | 0 |
| Label | Geist Mono | 13px | 400 | 1.5 | 0 |

- Labels are lowercase monospace, never all caps: `guidelines/`, `previous`, `copy`.
- Headings break into even lines (`text-wrap: balance`).
- Docs pages fill the space beside the menu, inside the page frame. The sign-in card is 520px wide.
- Turn off ligatures in code (`font-variant-ligatures: none`) so `->` shows as typed.
- On a phone (under 860px), H1 is 36px, H2 is 24px, and the lead is 17px.

## Spacing

A 4px base. Use these steps: 4, 8, 12, 16, 20, 24, 32, 40, 48, 56, 64, 96.

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
| Chips | 999px (pill) |
| Cards (sign-in, connect) | 16px |
| Panels, code blocks, the repository list, page links | 14px |
| Buttons, inputs, menu items | 10px |
| Small buttons | 8px |
| Inline code | 6px |
| Dots and avatars | 50% |

- One shadow, `--shadow-card`, lifts the sign-in card. Everything else is flat: hierarchy comes from `--tint` and `--soft` surfaces and 1px `--line` borders. No gradients.
- Orange dots (8px) mark state: a code block's language, a status line.

## Layout

| Region | Width |
|---|---|
| Top bar | full width, 72px tall (64px on a phone). Its logo and account line up with the page frame |
| Menu | 280px |
| Content | the rest of the page frame |
| Page frame | up to 1440px, centered in the window |

Under 860px, the menu moves behind a button in the top bar.

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
