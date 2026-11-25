---
verified: 2026-11-25
---


# UI kit

The components that make up Ohara's screens. Values reference the tokens in the [style guide](style-guide.md) and [`tokens.css`](tokens.css). The [mockups](mockups/) show them in context.

## Components

### Button

A rounded rectangle that says exactly what it does.

| Variant | Background | Text | Border | Use |
|---|---|---|---|---|
| Primary | `--clay` | `--on-clay`, Geist 600 | none | The one main action on a screen: "Sign in with GitHub", "Create GitHub App" |
| Secondary | `--bg-2` | `--ink`, Geist 500 | 1px `--line-strong` | Other actions: "Sign out" |
| Text | none | `--muted`, Geist Mono 13px | none | Small tools: "copy" |

- Height 44px, horizontal padding 20px, radius `--radius-control`. Small, in the top bar: 36px, padding 14px, radius `--radius-small`, 14px text.
- An optional 18px icon sits 10px before the label.
- Hover: primary turns `--clay-hover`; the secondary's border turns `--faint`; text turns `--ink`.
- Disabled: 55% opacity, `cursor: progress` while working ("Opening GitHub…").

```css
.button {
  display: inline-flex; align-items: center; justify-content: center; gap: 10px;
  min-height: 44px; padding: 0 20px;
  border: 1px solid transparent; border-radius: var(--radius-control);
  background: var(--clay); color: var(--on-clay);
  font: 600 15px var(--sans);
}
```

### Repository chip

Shows which repository the docs come from.

- Pill, `--border`, height 32px, padding 0 14px.
- Geist Mono 13px, `--muted`.
- A 13px lock icon before the name when the repository is private.

### Input

- Height 44px, padding 0 16px, `--bg-2` fill, 1px `--line-strong` border, radius `--radius-control`.
- Value in Geist Mono 14px, `--ink`. Placeholder in `--muted`.
- Label above it: Geist 14px 600, 8px gap.
- Focus: a 2px `--clay` outline with a 2px offset, as for every element.

### Repository list

A single choice among repositories.

- `--bg-2` panel, `--border`, radius `--radius-panel`, up to 336px tall, then it scrolls.
- One 48px row per repository, padding 0 16px, separated by `--border`: a radio with `accent-color: var(--clay)`, 12px gap, the name in Geist Mono 14px, and a 13px lock icon after it when the repository is private.
- Rows are `--muted`. Hover and the chosen row are `--ink`.
- Above 6 repositories, an input filters the list by name.

### Menu search

- Sits at the top of the menu. Height 40px (44px on a phone), padding 0 12px, `--bg-2` fill, `--border`, radius `--radius-control`.
- A 15px search icon, then the input in Geist 14px. Placeholder "Search docs" in `--muted`.
- A `⌘K` hint (`Ctrl K` off Mac) in Geist Mono 11px, hidden on a phone. The shortcut focuses the field, Escape clears it.
- Typing filters the menu by page title and keeps the folders that lead to each match.

### Menu item

| State | Style |
|---|---|
| Default | `--text`, Geist 14px, padding 8px 12px |
| Hover | `--ink` |
| Current | `--soft` background, `--ink`, weight 600, radius `--radius-control` |
| Current, nested | Same, with a 2px `--clay` bar over the guide line and square left corners |

### Menu folder

- A row with a 12px chevron in a 28px column, then the folder's title in Geist 14px 600, `--ink`. The title comes from the folder's index page, or from the folder name in sentence case.
- Clicking a folder without an index page folds or unfolds it. A folder with an index page links to it: opening the page unfolds the folder, clicking it again while there folds it. The chevron always folds or unfolds.
- When its index page is open, the folder row takes the menu item's Current style: `--soft` background, radius `--radius-control`.
- The chevron points down when open and turns -90° when folded.
- Children hang off a 1px `--line` guide line under the chevron, 18px in from the row. Nested pages are padded 14px from the line.

On a phone, every menu row is at least 44px tall.

### Breadcrumb

Geist Mono 13px, lowercase, segments joined by ` / `. Parents are `--muted` links. The current page is `--ink`.

### Code block

- `--tint` panel, `--border`, radius `--radius-panel`.
- Header bar: padding 12px 20px, bottom `--border`, Geist Mono 13px `--muted`. An orange dot and the language on the left, a "copy" text button on the right.
- Code: Geist Mono 14px, line height 1.7, padding 20px 24px, scrolls sideways.
- Highlighting: keywords `--link`, comments `--muted`, everything else `--ink`.

Inline code: Geist Mono 14px, `--tint`, `--border`, padding 1px 6px, radius `--radius-code`.

### Diagram

A ` ```mermaid ` block, drawn as a diagram.

- The code block's panel and header bar, with `mermaid` as the language. "copy" copies the Mermaid source.
- The diagram is centered, padding 24px, and shrinks to fit the content width.
- Nodes: `--bg-2` fill, `--clay` border, Geist 14px `--ink`. Clusters: `--soft` fill, `--border`. Lines and arrows `--muted`, edge labels `--text` on `--tint`.
- Mermaid loads only on a page that has a diagram. When the source doesn't parse, the block stays a code block.

### Quote

Geist 19px, `--ink`, with a 2px `--clay` bar on the left and 20px between the bar and the text.

### Task list

18px checkboxes with `accent-color: var(--clay)`, 12px before the text, no bullets.

### Table

Geist 15px. Header row weight 600 with a 1px `--line-strong` bottom border. Cells padding 9px 12px with a 1px `--line` bottom border. Wide tables scroll inside their own box.

### Page link (previous and next)

- Panel with `--border`, radius `--radius-panel`, padding 14px 18px, minimum width 180px. Hover: `--tint` background.
- Label (`previous`, `next`) in Geist Mono 13px `--muted`, the page title in Geist 16px 600 `--ink`.
- "Next" is right-aligned.

### Status line

A 8px orange dot, 8px gap, then Geist Mono 13px `--muted`: `acme/handbook is private`, `first launch`. Sits 16px above a display headline.

### Card

Holds the sign-in and connect screens. 520px wide, padding 40px (24px on a phone), `--bg-2` fill, `--border`, radius `--radius-card`, `--shadow-card`.

### Avatar

32px circle, `--soft` fill, the GitHub avatar image or the first letter of the login in Geist 13px 600.

### Logo

See the [brand guidelines](brand.md#logo). In the top bar the mark is 30px with the wordmark.

## Screens

### Docs reader

| Region | Content |
|---|---|
| Top bar | Logo, repository chip, then avatar, login and "Sign out" on the right. 72px tall, 1px `--line-soft` below, white at 90% over a blur of the page behind it |
| Menu | Search, "Overview", then one foldable group per folder. 280px, `--border` on the right |
| Content | Breadcrumb, H1, lead, page body, then "suggest a change on GitHub" and previous and next page links. Fills the rest of the page frame, padding 48px 64px |

On a phone the menu opens from a button in the top bar, covers the page and stops it from scrolling. Mockups: [`Main.dc.html`](mockups/Main.dc.html), [`Mobile.dc.html`](mockups/Mobile.dc.html).

### Sign in

Shown when the docs repository is private and the visitor isn't signed in. Logo top left. Below it, a centered card: status line, display headline "Sign in to read the docs", one sentence, primary button "Sign in with GitHub". Mockup: [`SignIn.dc.html`](mockups/SignIn.dc.html).

When a signed-in user can't read the repository, the same layout says "You don't have access", names their login and the repository, and offers a secondary "Sign out" button.

### Connect a coding assistant

Shown after GitHub sign-in when a coding assistant asks for access through MCP. Same layout as sign in: status line `signed in as <login>`, display headline "Connect <client>?", then one sentence naming the repository, the login it acts as, and the address it returns to in inline code, and a warning to connect only an app the user started. A primary "Connect" button and a secondary "Cancel" button sit side by side, 12px apart. Both are disabled while the answer is sent.

When the request has expired, the same layout says "This request expired" and asks the user to connect again from their coding assistant.

### Setup

Shown on first launch. Two columns: on the left the status line `first launch`, the page headline "Connect your docs repository" and one sentence on access, as a lead. On the right, two steps separated by `--border` rules and numbered `01` and `02` in Geist Mono 13px. The current step's number is `--link`.

- **01 Create and install the GitHub App:** the organization input and the "Create GitHub App" button. Once the app exists and isn't installed yet, a primary "Install on GitHub" button and a secondary "Check again" button, 12px apart.
- **02 Choose the docs repository:** once the app is installed, the repository list, then a primary "Use this repository" button and a secondary "Change repositories on GitHub" button.

Mockup: [`Setup.dc.html`](mockups/Setup.dc.html).

### Empty and error states

An H1 says what's missing, and one sentence says how to fix it. The docs states use the same layout as the docs reader content:

- No root page: "Add an overview page". Create a `README.md` at the root of the repository.
- Page not found: "This page doesn't exist". It may have been moved or renamed. Link to the overview.

When the server can't be reached, nothing of the docs reader can load, so this state uses the sign-in layout:

- Server unreachable: "Ohara can't be reached". Reload the page in a moment.
