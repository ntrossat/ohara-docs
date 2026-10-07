---
order: 7
verified: 2026-10-07
---

# Writing docs

## Where docs live

- A code repository keeps the docs about itself in `docs/` and lists them in `.ohara.yml`. Ohara syncs them into `apps/<repo>/` here. Edit them in the code repository, in the same change as the code.
- Everything else (guidelines, design, team pages) lives in this repository. Change it through Ohara's `propose_change` or a pull request.
- A code repository's `README.md` covers principles and features only. Architecture and setup go in its `docs/`.

## Format

- Plain Markdown. No HTML: the reader doesn't render it.
- Each page starts with one `#` heading, its title. The folder tree is the menu, and a folder's `README.md` is its index page.
- Front matter is optional: `order` sets the menu order, `owner` names who keeps the page true, `covers` links it to the code it describes.
- Link pages with relative paths to the `.md` file.
- Give every code block a language.

## Voice

Write like a good senior engineer, following the [voice](../design/brand.md#voice) and the [copy rules](../design/style-guide.md#copy):

- Short sentences, common words, active voice.
- Say what happens and what to do. No hype, no exclamation marks, no apologies.
- Sentence case for headings.
- Tables for options and reference, numbered lists for steps.
