---
verified: 2026-11-27
---

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
- Hover: primary turns `--clay-hover`; secondary and text turn `--ink`.
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
- Focus: a 2px `--clay` outline with a 2px offset, as for every element.

### Repository list

A single choice among repositories.

- `--bg-2` panel, `--border`, radius `--radius-panel`, up to 336px tall, then it scrolls.
- One 48px row per repository, padding 0 16px, separated by `--border`: a radio with `accent-color: var(--clay)`, 12px gap, the name in JetBrains Mono 14px, and a 13px lock icon after it when the repository is private.
- Rows are `--muted`. Hover and the chosen row are `--ink`.
- Above 6 repositories, an input filters the list by name.

### Menu search

- Sits at the top of the menu. Height 40px (44px on a phone), padding 0 12px, `--bg-2` fill, `--border`, radius `--radius-item`.
- A 15px search icon, then the input in Manrope 14px. Placeholder "Search docs" in `--muted`.
- A `⌘K` hint (`Ctrl K` off Mac) in JetBrains Mono 11px, hidden on a phone. The shortcut focuses the field, Escape clears it.
- Typing filters the menu by page title and keeps the folders that lead to each match.

### Menu item

| State | Style |
|---|---|
| Default | `--muted`, Manrope 14px, padding 8px 12px |
| Hover | `--ink` |
| Current | `--bg-2` background, `--ink`, weight 600, radius `--radius-item` |
| Current, nested | Same, with a 2px `--clay` bar over the guide line and square left corners |

### Menu folder

- A row with a 12px chevron in a 28px column, then the folder's title in Manrope 14px 600, `--ink`. The title comes from the folder's index page, or from the folder name in sentence case.
- Clicking a folder without an index page folds or unfolds it. A folder with an index page links to it: opening the page unfolds the folder, clicking it again while there folds it. The chevron always folds or unfolds.
- When its index page is open, the folder row takes the menu item's Current style: `--bg-2` background, radius `--radius-item`.
- The chevron points down when open and turns -90° when folded.
- Children hang off a 1px `--line` guide line under the chevron, 18px in from the row. Nested pages are padded 14px from the line.

On a phone, every menu row is at least 44px tall.

### Count

The number of changes waiting for review, at the right end of the "Changes" menu item.

- Pill, `--clay-soft` fill, `#9a3412` text (`--link` is too light on this fill), 13px weight 600, at least 22px wide, padding 0 7px.
- Hidden when nothing waits.

### Badge

Says what a change does to a file: `changed`, `new page`, `removed`.

- Pill, `--clay-soft` fill, `#9a3412` text, 13px weight 600, padding 2px 12px.
- A 6px clay dot, 8px before the label.

### Breadcrumb

JetBrains Mono 12px, lowercase, segments joined by ` / `. Parents are `--muted` links. The current page is `--ink`.

### Code block

- `--bg-2` panel, `--border`, radius `--radius-panel`.
- Header bar: padding 12px 20px, bottom `--border`, JetBrains Mono 12px `--muted`. A clay dot and the language on the left, a "copy" text button on the right.
- Code: JetBrains Mono 14px, line height 1.7, padding 20px 24px, scrolls sideways.
- Highlighting: keywords `--clay`, comments `--muted`, everything else `--ink`.

Inline code: JetBrains Mono 14px, `--bg-2`, `--border`, padding 1px 6px, radius `--radius-code`.

### Diagram

A ` ```mermaid ` block, drawn as a diagram.

- The code block's panel and header bar, with `mermaid` as the language. "copy" copies the Mermaid source.
- The diagram is centered, padding 24px, and shrinks to fit the content width.
- Nodes: `--surface-3` fill, `--clay` border, Manrope 14px `--ink`. Clusters: `--bg` fill, `--border`. Lines and arrows `--muted`, edge labels `--text` on `--bg-2`.
- Mermaid loads only on a page that has a diagram. When the source doesn't parse, the block stays a code block.

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

The list of changes uses the same panel, full width: the change's title, then a label with its number, who proposed it, and the date.

### Change card

One file of a change, shown as the page reads after it, with its unchanged sections folded.

- Card with `--border`, radius `--radius-card`, `--bg-2` fill, `--shadow-card`, 32px below.
- Header bar: padding 12px 24px, bottom `--border`, `--tint` fill. The file path in JetBrains Mono 13px `--muted`, its folders joined by ` / ` and the file name in `--ink`, and a badge on the right.
- Body: padding 32px, the page rendered as in the docs reader. Its H1 steps down to the H2 size and its H2 to the H3 size, and the lead reads as body text, so the change's own title leads the screen.
- A file that is not a page shows one sentence that sends to GitHub.

### Change fold

A run of three or more unchanged blocks, folded inside a change card. The page's title and the headings right above a change stay visible.

- A full-width button, 44px tall, padding 0 16px, 1px dashed `--line-strong` border, radius `--radius-control`, `--tint` fill, 20px below.
- A 12px chevron, then "Unchanged: Button to Menu folder", naming the first and last sections it holds, in JetBrains Mono 13px `--muted`, then "Show" in `--ink` on the right.
- Open, the chevron turns 90°, the label on the right says "Hide", and the folded text renders below.

### Change marks

What a change removes and adds, inside the rendered page.

| Mark | Style |
|---|---|
| Removed words | `--muted`, struck through in `--clay` |
| Added words | `--clay-soft` background, `--ink`, padding 1px 4px, radius `--radius-code`. 0.3em after removed words |
| Removed block | The whole block in `--muted`, struck through in `--clay` |
| Added block | `#fff5ee` panel with a 1px `#ffd9c2` border, radius `--radius-control`, padding 20px 24px. Inline code inside it is on `--bg-2` |

Words are marked in paragraphs, headings, lists and table cells. Code blocks and HTML show whole, as a removed block then an added one. A new page has no marks: its badge says `new page`.

### Change note

The description of a change, above its cards: `--soft` panel, radius `--radius-panel`, padding 20px 24px, 15px text, under the title "Why this changed" in `--ink` weight 600.

### Approval bar

The actions of a change, pinned 16px above the bottom of the window while the change is open.

- Panel with `--border`, radius `--radius-card`, `--bg-2` fill, `--shadow-card`, padding 12px 12px 12px 24px.
- One 14px sentence on the left, then a secondary "View on GitHub" button and the primary button, 12px apart. On a narrow screen the buttons wrap under the sentence.
- The sentence says what approving does: "Approving merges this change into `<repository>`." When GitHub refuses the merge, GitHub's reason replaces it, in `--error`.

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
| Menu | Search, "Overview", "Changes" with its count, then one foldable group per folder. 280px, `--border` on the right |
| Content | Breadcrumb, H1, lead, page body, then "suggest a change on GitHub" and previous and next page links. Fills the rest of the page frame, padding 48px 64px |

On a phone the menu opens from a button in the top bar, covers the page and stops it from scrolling. Mockups: [`Main.dc.html`](mockups/Main.dc.html), [`Mobile.dc.html`](mockups/Mobile.dc.html).

### Changes

The open pull requests of the docs repository, in the docs reader's content region, at `/-/changes`.

- H1 "Changes", then a lead that names the repository and says that approving a change merges it.
- One page link panel per change, 12px apart, the latest change first.
- Nothing to review: one sentence in place of the list.

### Change

One pull request, at `/-/changes/<number>`.

- Breadcrumb `changes / #482`, the pull request's title as H1, then one row: a 32px avatar with the proposer's initial and "Proposed by **`<login>`** · `<date>`" ("Draft, proposed by" for a draft), a chip with the number of pages, and on the right a key of the two marks, "removed" and "added", when a page is changed.
- The change note with the pull request's description, when it has one.
- One change card per file.
- The approval bar. Its primary button is "Approve change", with a check icon. While the approval is sent, it is disabled and says "Approving…".
- A visitor who isn't signed in gets "Sign in to approve" as the primary button. A signed-in user without write access, or a draft, gets no primary button, and the bar's sentence says why.
- Once approved, a notice replaces the bar: "Approved and merged. The docs update in a moment.", with a link to the other changes. A change already merged or closed shows a notice in the same place.

### Sign in

Shown when the docs repository is private and the visitor isn't signed in. Logo top left. Bottom left: status line, display headline "Sign in to read the docs", one sentence, primary button "Sign in with GitHub". Mockup: [`SignIn.dc.html`](mockups/SignIn.dc.html).

When a signed-in user can't read the repository, the same layout says "You don't have access", names their login and the repository, and offers a secondary "Sign out" button.

### Connect a coding assistant

Shown after GitHub sign-in when a coding assistant asks for access through MCP. Same layout as sign in: status line `signed in as <login>`, display headline "Connect <client>?", then one sentence naming the repository, the login it acts as, and the address it returns to in inline code, and a warning to connect only an app the user started. A primary "Connect" button and a secondary "Cancel" button sit side by side, 12px apart. Both are disabled while the answer is sent.

When the request has expired, the same layout says "This request expired" and asks the user to connect again from their coding assistant.

### Setup

Shown on first launch. Two columns: on the left the status line `first launch`, the headline "Connect your docs repository" and one sentence on access. On the right, two steps separated by `--border` rules and numbered `01` and `02` in JetBrains Mono 12px. The current step's number is clay.

- **01 Create and install the GitHub App:** the organization input and the "Create GitHub App" button. Once the app exists and isn't installed yet, a primary "Install on GitHub" button and a secondary "Check again" button, 12px apart.
- **02 Choose the docs repository:** once the app is installed, the repository list, then a primary "Use this repository" button and a secondary "Change repositories on GitHub" button.

Mockup: [`Setup.dc.html`](mockups/Setup.dc.html).

### Empty and error states

A Newsreader H1 says what's missing, and one sentence says how to fix it. The docs states use the same layout as the docs reader content:

- No root page: "Add an overview page". Create a `README.md` at the root of the repository.
- Page not found: "This page doesn't exist". It may have been moved or renamed. Link to the overview.
- Change not found: "This change doesn't exist". The repository has no pull request with this number. Link to the changes.

When the server can't be reached, nothing of the docs reader can load, so this state uses the sign-in layout:

- Server unreachable: "Ohara can't be reached". Reload the page in a moment.
