---
covers: [ntrossat/ohara:backend/ohara/mcp_server.py, ntrossat/ohara:backend/ohara/oauth.py]
verified: 2026-10-06
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
| Public | None to read. Sign in to propose changes |
| Private | On first use, the assistant opens a GitHub sign-in in the browser. Only people who can read the repository get access |

After GitHub sign-in, Ohara names the assistant and the address it returns to, and asks you to connect it. Connect only an assistant you started yourself: a sign-in link from someone else would give them your access.

For CI and headless agents, send a GitHub token instead of signing in:

```bash
claude mcp add --transport http ohara <OHARA_URL>/mcp --header "Authorization: Bearer <token>"
```

The token needs read access to the docs repository, and write access to propose changes.

Sign-in in the browser requires `OHARA_URL` to start with `https://`, or to be `localhost`. Otherwise only GitHub tokens work.

## Tools

| Tool | What it does |
|---|---|
| `list_pages` | Every page with its path and title |
| `search` | Pages that contain every word of a query, best matches first, with why each may be stale |
| `read_page` | A page's Markdown, owner, verified date, and why it may be stale. An empty path is the home page |
| `stale_pages` | Pages that may be out of date, with the reasons |
| `propose_change` | Opens a pull request with new or changed pages |

See [Track freshness](docs-repository.md#track-freshness) for what makes a page stale.

## Propose changes

An assistant sends a title, a description, and the full new Markdown of each page, front matter included. A page path can be an existing page or a new one, such as `team/onboarding`.

Ohara then:

1. checks that the signed-in user, or the token, can write to the docs repository;
2. sets each page's `verified` date to today;
3. commits the pages on a new `ohara/…` branch and opens a pull request, signed "Proposed through Ohara by @login".

A human reviews and merges the pull request. Merging updates the website and verifies the pages.

The GitHub App opens the pull request, so the person who proposed it can still review and approve it.
