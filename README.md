---
verified: 2026-10-07
---

# Ohara

*One central place for all enterprise knowledge.*

This repository holds everything the Ohara project knows: its product docs, its design, and its guidelines. AI keeps it up to date. A human approves every change.

**AI-generated, human-controlled.**

---

## Start here

| You want to | Read |
|---|---|
| Understand what Ohara is and who it helps | [Ohara](apps/ohara/README.md) |
| Learn the concepts and the vocabulary | [How Ohara works](apps/ohara/concepts.md) |
| Run Ohara for your team | [Install](apps/ohara/install/README.md), then [Configure](apps/ohara/configure/README.md) |
| Work with Ohara from a coding assistant | [Use](apps/ohara/use/README.md) |
| Change Ohara's code | [Developers](apps/ohara/developers/README.md) |
| Design for Ohara | [Design](design/README.md) |

---

## What's inside

```text
.
├── apps/
│   └── ohara/     Product docs, synced from the ohara code repository
└── design/        Brand, style guide, UI kit, design tokens, and logos
```

The folder tree is the site menu. Each page's first heading is its title.

---

## How this repository works

1. **AI proposes.** Coding assistants and agents propose changes as pull requests, through Ohara's MCP server.
2. **A human reviews.** Every pull request is read and approved before it merges.
3. **Merging publishes.** Each merge to `main` rebuilds the site and verifies the pages it changes.

Pages under `apps/` come from code repositories, which keep their own docs next to their code. Edit them there: Ohara syncs them here on each push.

---

## Contribute

- **From a coding assistant:** connect Ohara and run `/ohara:update` after a code change, or `/ohara:ingest` to bring in docs from other tools.
- **By hand:** open a pull request on this repository.

Either way, a human approves the change before it merges.

---

## License

[Apache-2.0](https://github.com/ntrossat/ohara/blob/main/LICENSE)
