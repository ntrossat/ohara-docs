# UI kit

The components that make up Ohara's screens. Values reference the tokens in the [style guide](style-guide.md) and [`tokens.css`](tokens.css). The [mockups](mockups/) show them in context.

## Components

### Button

A pill that says exactly what it does.

| Variant | Background | Text | Border | Use |
|---|---|---|---|---|
| Primary | `--clay` | `--on-clay`, Manrope 600 | none | The one main action on a screen: "Sign in with GitHub", "Create GitHub App" |
| Secondary | transparent | `--muted`, Manrope 500 | `--border` | Other actions: "Sign out" |
| Text | none | `--muted`, JetBrains Mono 12px | none | Small tools: "copy" |

- Height 48px (primary), 36px (secondary in the top bar). Horizontal padding 24px and 16px.
- Radius `--radius-pill`. An optional 18px icon sits 10–12px before the label.
- Hover: primary lightens to `#E2876A`; secondary and text turn `--ink`.
- Disabled: 60% opacity, `cursor: progress` while working ("Opening GitHub…").

```css
.button {
  display: inline-flex; align-items: center; gap: 12px;
  min-height: 48px; padding: 0 24px;
  border: 0; border-radius: var(--radius-pill);
  background: var(--clay); color: var(--on-clay);
  font: 600 15px var(--sans);
}
```

### Repository chip

Shows which repository the docs come from.

- Pill, `--border`, height 32px, padding 0 14px.
- JetBrains Mono 12px, `--muted`.
- A 13px lock icon before the name when the repository is private.

### Input

- Height 48px, padding 0 16px, `--bg-2` fill, `--border`, radius `--radius-panel`.
- Value in JetBrains Mono 14px, `--ink`. Placeholder in `--muted`.
- Label above it: Manrope 14px 600, 8px gap.
- Focus: `--clay` border.

### Menu item

| State | Style |
|---|---|
| Default | `--muted`, Manrope 15px, padding 8px 12px |
| Hover | `--ink` |
| Current | `--bg-2` background, `--ink`, weight 600, radius `--radius-item`, a 7px clay dot 10px before the title |

Folder headings are JetBrains Mono 12px `--muted`, written as the folder path (`guidelines/`), with 24px above.

### Breadcrumb

JetBrains Mono 12px, lowercase, segments joined by ` / `. Parents are `--muted` links. The current page is `--ink`.

### On this page

- Label `on this page`, JetBrains Mono 12px, `--muted`.
- A 1px `--line` rule on the left. Links are Manrope 14px `--muted`, padding 6px 0 6px 14px.
- The current section is `--ink` with a 1px `--clay` rule.

### Code block

- `--bg-2` panel, `--border`, radius `--radius-panel`.
- Header bar: padding 12px 20px, bottom `--border`, JetBrains Mono 12px `--muted`. A clay dot and the language on the left, a "copy" text button on the right.
- Code: JetBrains Mono 14px, line height 1.7, padding 20px 24px, scrolls sideways.
- Highlighting: keywords `--clay`, comments `--muted`, everything else `--ink`.

Inline code: JetBrains Mono 14px, `--bg-2`, `--border`, padding 1px 6px, radius `--radius-code`.

### Quote

Newsreader italic 24px weight 300, `--ink`, no border or background.

### Task list

18px checkboxes with `accent-color: var(--clay)`, 12px before the text, no bullets.

### Table

Manrope 15px. Header row weight 700 with a 2px `--line` bottom border. Cells padding 9px 12px with a 1px `--line` bottom border. Wide tables scroll inside their own box.

### Page link (previous and next)

- Panel with `--border`, radius `--radius-panel`, padding 14px 18px, minimum width 180px.
- Label (`previous`, `next`) in JetBrains Mono 12px `--muted`, the page title in Newsreader 20px `--ink`.
- "Next" is right-aligned.

### Status line

A 8px clay dot, 12px gap, then JetBrains Mono 12px `--muted`: `acme/handbook is private`, `first launch`. Sits above a display headline.

### Avatar

32px circle, `--surface-3` fill, the GitHub avatar image or the first letter of the login in Manrope 13px 600.

### Logo

See the [brand guidelines](brand.md#logo). In the top bar the mark is 30px with the wordmark.

## Screens

### Docs reader

| Region | Content |
|---|---|
| Top bar | Logo, repository chip, then avatar, login and "Sign out" on the right. 72px tall, `--border` below |
| Menu | "Overview", then one group per folder. 280px, `--border` on the right |
| Content | Breadcrumb, H1, lead, page body, then "suggest a change on GitHub" and previous and next page links. Up to 720px, padding 48px 64px |
| On this page | One link per H2. 220px |

On a phone the menu opens from a button in the top bar and "On this page" is hidden. Mockups: [`Main.dc.html`](mockups/Main.dc.html), [`Mobile.dc.html`](mockups/Mobile.dc.html).

### Sign in

Shown when the docs repository is private and the visitor isn't signed in. Logo top left. Bottom left: status line, display headline "Sign in to read the docs", one sentence, primary button "Sign in with GitHub". Mockup: [`SignIn.dc.html`](mockups/SignIn.dc.html).

When a signed-in user can't read the repository, the same layout says "You don't have access", names their login and the repository, and offers a secondary "Sign out" button.

### Setup

Shown on first launch. Two columns: on the left the status line `first launch`, the headline "Connect your docs repository" and one sentence on access. On the right, two steps separated by `--border` rules and numbered `01` and `02` in JetBrains Mono 12px. The current step's number is clay. Step one holds the organization input and the "Create GitHub App" button. Mockup: [`Setup.dc.html`](mockups/Setup.dc.html).

### Empty and error states

Same layout as the docs reader content. A Newsreader H1 says what's missing, and one sentence says how to fix it:

- No root page: "Add an overview page". Create a `README.md` at the root of the repository.
- Page not found: "This page doesn't exist". It may have been moved or renamed. Link to the overview.
- Server unreachable: "Ohara can't be reached". Reload the page in a moment.
