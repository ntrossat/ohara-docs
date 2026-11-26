---
verified: 2026-11-25
---


# Brand guidelines

## Name

**Ohara**: one central place for all enterprise knowledge. Write it with one capital: Ohara.

Tagline: **One central place for all enterprise knowledge.**

Principle: **AI keeps your docs up to date. You approve every change.**

## Logo

The logo is a mountain: the island of Ohara, where the Tree of Knowledge lives in a famous manga.

- The mountain is one filled shape, `M3 25L12 9l5 8 3-4 9 12z` on a 32-unit grid.
- It takes the color of the text around it: `--ink` (`#18181B`) on a light background.
- There is no background shape.

| Version | File | Use |
|---|---|---|
| Mark | [`logo.svg`](../resources/logo.svg) | Favicon, app icon, small spaces |
| Mark with wordmark | [`logo-wordmark.svg`](../resources/logo-wordmark.svg) | Top bar, sign-in, setup, anything where the name must be read |

The wordmark is "Ohara" in Geist 700 at 21px, letter-spacing -0.045em, with 10px between the mark and the word at a 30px mark.

### Logo rules

- Keep clear space around the logo equal to half the mark's height.
- Minimum size: 16px for the mark, 24px mark height with the wordmark.
- Use it on `--bg` (`#FFFFFF`) or `--tint` (`#FAFAFA`). On a dark background, the mountain and the wordmark turn white.
- Don't add a background shape, outline, shadow, or gradient, or rotate or stretch the mark.

## Colors

Ohara is light: white and near-black, with orange as the one accent.

| Name | Value | Role |
|---|---|---|
| White | `#FFFFFF` | Background, panels, and cards |
| Near-black | `#18181B` | Titles, primary text, and the logo |
| Grey | `#63636B` | Secondary text |
| Light grey | `#FAFAFA` | Code blocks and quiet cards |
| Orange | `#FF6B1A` | The accent |

There is no blue. Orange is used sparingly: the main button on a screen, the current page, small status dots. Never fill large areas with it, and never set small text in it: links and code keywords use the darker `--link`.

The full palette and its tokens are in the [style guide](style-guide.md#color).

## Typefaces

| Typeface | Role |
|---|---|
| [Geist](https://fonts.google.com/specimen/Geist) | Titles, body text, and interface. Titles are semibold with tight letter spacing |
| [Geist Mono](https://fonts.google.com/specimen/Geist+Mono) | Code and small labels |

Both are open source (SIL Open Font License).

## Voice

Ohara speaks like a good senior engineer: clear, calm, and precise.

- **Plain:** short sentences, common words, active voice.
- **Direct:** say what happens. A button says "Sign in with GitHub", not "Continue".
- **Calm:** no exclamation marks, no hype, no apologies. Errors say what went wrong and how to fix it.
- **Human about AI:** AI keeps your docs up to date. You approve every change. Never present AI output as final.

| Instead of | Write |
|---|---|
| Oops! Something went wrong. | Ohara couldn't reach GitHub. Reload the page in a moment. |
| Supercharge your docs with AI! | AI keeps your docs up to date. You approve every change. |
| Submit | Create GitHub App |
