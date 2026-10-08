---
verified: 2026-10-08
---

# Ohara

**One central place for all enterprise knowledge. AI-generated, human-controlled.**

Ohara keeps your docs and engineering rules in one place, for your team and for your AI assistants. Your team reads them on a website. Your assistants follow them when they write code, and propose a doc update each time they change the code. A person approves every change.

Ohara is open source. It runs on your own servers, and your docs stay in your GitHub repository.

*You are reading Ohara now: this site runs on it.*

## Why teams use Ohara

| Without Ohara | With Ohara |
|---|---|
| Docs are spread across Confluence, Google Drive, Jira, Slack, and GitHub. People can't find the answer, or find three that disagree. | Every doc and rule lives in one place. Ohara imports what you already have. |
| Each developer sets up their AI assistant alone. The assistants follow different rules, and the code drifts apart. | Architects write the rules once. Every assistant, in every project, follows them. |
| The docs fall behind the code. Nobody trusts them, so nobody reads them. | The assistant updates the docs in the same change as the code. Ohara flags the pages that fall behind. |

## How it works: an example

An engineer asks Claude Code to add refunds to the billing service.

1. **The assistant reads your rules first.** It looks up the guidelines and docs that apply in Ohara, such as your API style and your security rules.
2. **It writes the code,** then checks it against those rules.
3. **It updates the docs.** It sends the new billing pages to Ohara, which opens a pull request: a proposed change that waits for a person's review.
4. **A person reviews the code and its docs together,** and merges both.
5. **Everyone gets the new docs within seconds,** on the website and in their assistants.

If someone later changes the billing code and not its docs, Ohara flags the pages that describe it. Assistants see the flag and propose the update.

## Where you use it

| Where | What you do |
|---|---|
| The website | Read and search the docs. Private docs ask you to sign in with GitHub. |
| Claude Code and other coding assistants | Code with an assistant that follows your rules and proposes the doc updates. |
| claude.ai and ChatGPT | Ask in plain language, such as "What are our security guidelines?", and import docs from Confluence or Google Drive. |

Assistants connect through MCP, the open standard that connects AI assistants to tools.

## Why you can trust it

- **A person approves every change.** AI only proposes. Each change is a pull request that someone on your team reviews and merges. Ohara never merges on its own.
- **Your docs stay in your GitHub repository,** as plain Markdown files with their full history.
- **It runs on your servers.** One container, set up in about five minutes.
- **Access follows GitHub.** People who can read the repository can read the docs. There are no other accounts to manage.
- **Open source.** Apache-2.0 license.

## Start here

| You want to | Read |
|---|---|
| See what Ohara does and who it helps | [Overview](apps/ohara/README.md) |
| Understand how it works | [How Ohara works](apps/ohara/concepts.md) |
| Ask questions from claude.ai or ChatGPT | [Chat apps](chat-apps.md) |
| Run Ohara for your team | [Install](apps/ohara/install/README.md), then [Configure](apps/ohara/configure/README.md) |
| Connect a coding assistant | [Coding assistants](apps/ohara/use/coding-assistants.md) |
| Set up the team workflow | [Team workflow](apps/ohara/use/team-workflow.md) |
| Change Ohara's code | [Guidelines](guidelines/README.md), then [Developers](apps/ohara/developers/README.md) |
| Design for Ohara | [Design](design/README.md) |

## About this site

This site is the Ohara project's own docs, served by Ohara. The folder tree is the menu.

| Folder | Holds |
|---|---|
| `guidelines/` | Engineering rules every project follows |
| `apps/ohara/` | Product docs, synced from the [ohara code repository](https://github.com/ntrossat/ohara) |
| `design/` | Brand, style guide, UI kit, design tokens, and logos |
| `references/` | Reference material, such as the FastAPI user guide |

To contribute:

- **From a coding assistant:** connect Ohara, then run `/ohara:update` after a code change, or `/ohara:ingest` to bring in docs from other tools.
- **By hand:** open a pull request on this repository. Pages under `apps/` are edited in the ohara code repository, next to the code.

Either way, a person approves the change before it merges. Each merge to `main` updates this site.

## License

[Apache-2.0](https://github.com/ntrossat/ohara/blob/main/LICENSE)
