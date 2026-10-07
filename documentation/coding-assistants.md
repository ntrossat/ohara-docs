---
covers:
  - ntrossat/ohara:backend/ohara/mcp_server.py
  - ntrossat/ohara:backend/ohara/oauth.py
verified: 2026-10-07
---

# Connect a coding assistant

Ohara serves the docs to AI agents and coding assistants through an MCP server at `OHARA_URL/mcp`. Assistants search and read pages, see which ones are stale, and propose changes as pull requests for a human to review.

## Connect

```bash
claude mcp add --transport http ohara <OHARA_URL>/mcp
```

Any MCP client that supports the Streamable HTTP transport works the same way.

| Docs repository | Sign-in |
|---|---|
| Public | None to read. Sign in to propose changes and check a repository |
| Private | On first use, the assistant opens a GitHub sign-in in the browser. Only people who can read the repository get access |

After GitHub sign-in, Ohara names the assistant and the address it returns to, and asks you to connect it. Connect only an assistant you started yourself: a sign-in link from someone else would give them your access.

For CI and headless agents, send a GitHub token instead of signing in:

```bash
claude mcp add --transport http ohara <OHARA_URL>/mcp --header "Authorization: Bearer <token>"
```

The token needs read access to the docs repository, and write access to propose changes.

Sign-in in the browser requires `OHARA_URL` to start with `https://`, or to be `localhost`. Otherwise only GitHub tokens work.

## Set up a project

In Claude Code, run `/ohara:init` in the project. The assistant first checks that the Ohara GitHub App is installed on the project's repository. If it is not, the assistant opens the app's installation settings on GitHub: an admin of the account adds the repository, and the assistant checks again. Without it, pushes don't flag stale pages, docs pull requests don't merge with the code, and the project's own docs can't be synced. You can skip this step and connect the repository later.

The app is private to the account that created it, so only that account's repositories can be connected.

When the project keeps docs in its repository, the assistant offers to write a `.ohara.yml` that lists them, `docs/` by default. Only projects with that file have their docs synced into `apps/<repository name>/`. See [Sync docs from code repositories](docs-repository.md#sync-docs-from-code-repositories).

The assistant then adds the Ohara server to `.mcp.json`, writes an "Ohara instructions" section in `CLAUDE.md` with the guidelines and docs that apply and the workflow, and allows the read-only Ohara tools. The workflow updates the project's synced docs in the same change as the code, and proposes every other page to Ohara.

## Tools

| Tool | What it does |
|---|---|
| `list_pages` | Every page with its path and title, and the source of synced pages |
| `search` | Pages that contain every word of a query, best matches first, with why each may be stale |
| `read_page` | A page's Markdown, owner, verified date, covered code, why it may be stale, and for a synced page its source file. An empty path is the home page |
| `stale_pages` | Pages that may be out of date, with the reasons |
| `check_repository` | Whether the Ohara GitHub App is installed on a code repository and where to add it if not, and the paths of its synced docs |
| `propose_change` | Opens pull requests with new or changed pages |

See [Track freshness](docs-repository.md#track-freshness) for what makes a page stale.

## Propose changes

An assistant sends a title, a description, and the full new Markdown of each page, front matter included. A page path can be an existing page or a new one, such as `team/onboarding`. It also sends the project's repository name and active git branch, so every change from one code branch lands in the same pull requests.

Ohara then:

1. checks that the signed-in user, or the token, can write to the repositories the pages go to;
2. sets each page's `verified` date to today;
3. commits the pages on a branch of the docs repository. From a code branch, pages without a code owner in the docs repository's `CODEOWNERS` go to `project/branch`, and pages with one go to `project/branch-review`. Other proposals get their own `ohara/…` branch. If a branch already has an open pull request, the pages are added to it. Otherwise Ohara opens one, signed "Proposed through Ohara by @login".

Synced pages, under `apps/`, follow their source:

| Page | Where the change goes |
|---|---|
| From the current project | Refused: the assistant edits the source file in the project, in the same change as the code |
| From another code repository | A pull request on that repository, at the source file, without the `source` field |
| A new page under `apps/` | Refused: new pages go in the code repository's docs |

Ohara returns the pull request links. The assistant puts them in the code pull request's description, so the code reviewers see the docs changes.

The `project/branch` pull request merges when the code merges, and closes when the code pull request closes without merging. A human reviews and merges the others. Merging updates the website and verifies the pages. See [Merge docs with code](merge-with-code.md).

The GitHub App opens the pull request, so the person who proposed it can still review and approve it.
