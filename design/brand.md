# Brand guidelines

## Name

Write the name "Ohara", with a capital O and the rest in lowercase.

Tagline: **AI-generated, human-controlled.**

## Logo

The logo is `</>`, the sign of code, because Ohara treats documentation as code.

- The brackets are `--ink` (`#FAF9F5`).
- The slash is `--clay` (`#D97757`), the only accent color.
- Strokes are 2.4 units on a 32-unit grid, with round caps and joins.
- There is no background shape.

| Version | File | Use |
|---|---|---|
| Mark | [`logo.svg`](logo.svg) | Favicon, app icon, small spaces |
| Mark with wordmark | [`logo-wordmark.svg`](logo-wordmark.svg) | Top bar, sign-in, setup, anything where the name must be read |

The wordmark is "Ohara" in Manrope 600, letter-spacing 0.06em, with 10px between the mark and the word at a 30px mark.

### Logo rules

- Keep clear space around the logo equal to half the mark's height.
- Minimum size: 16px for the mark, 24px mark height with the wordmark.
- Use it on `--bg` (`#141413`) or `--bg-2` (`#1C1B19`). On a light background, swap the brackets to `#141413` and keep the slash clay.
- Don't recolor the slash, add a background shape, outline, shadow, or gradient, or rotate or stretch the mark.

## Colors

Ohara is dark by default, with one warm accent.

| Name | Value | Role |
|---|---|---|
| Ink black | `#141413` | Background |
| Ink black 2 | `#1C1B19` | Surfaces |
| Paper | `#FAF9F5` | Titles and primary text |
| Stone | `#A6A39A` | Secondary text |
| Clay | `#D97757` | The accent |

There is no blue. Clay is used sparingly: buttons, the current page, small status dots, code keywords. Never fill large areas with it.

The full palette and its tokens are in the [style guide](style-guide.md#color).

## Typefaces

| Typeface | Role |
|---|---|
| [Newsreader](https://fonts.google.com/specimen/Newsreader) | Titles and quotes. Light weights give Ohara its editorial voice |
| [Manrope](https://fonts.google.com/specimen/Manrope) | Body text and interface |
| [JetBrains Mono](https://fonts.google.com/specimen/JetBrains+Mono) | Code and small labels |

All three are open source (SIL Open Font License).

## Voice

Ohara speaks like a good senior engineer: clear, calm, and precise.

- **Plain:** short sentences, common words, active voice.
- **Direct:** say what happens. A button says "Sign in with GitHub", not "Continue".
- **Calm:** no exclamation marks, no hype, no apologies. Errors say what went wrong and how to fix it.
- **Human about AI:** AI proposes, humans approve. Never present AI output as final.

| Instead of | Write |
|---|---|
| Oops! Something went wrong. | Ohara couldn't reach GitHub. Reload the page in a moment. |
| Supercharge your docs with AI! | AI keeps your docs up to date. You approve every change. |
| Submit | Create GitHub App |
