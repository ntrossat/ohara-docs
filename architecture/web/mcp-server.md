---
covers: [ntrossat/ohara:backend/ohara/mcp_server.py, ntrossat/ohara:backend/ohara/freshness.py, ntrossat/ohara:backend/ohara/docs.py]
verified: 2026-10-06
---

# MCP server

The MCP server at `/mcp` gives AI agents and coding assistants the same docs as the website. It runs in the same FastAPI process, uses the Streamable HTTP transport, and is stateless: every request stands alone. To connect a client, see [Connect a coding assistant](../../documentation/coding-assistants.md).

## Access

Access mirrors the website: a public docs repository is open to everyone, a private one requires a bearer token from a user who can read it. The token is an Ohara `oha_` token from the OAuth sign-in, or a GitHub token. See [MCP sign-in](authentication.md#mcp-sign-in).

## Tools

| Tool | Reads | Returns |
|---|---|---|
| `list_pages` | The menu of the current snapshot | Each page's path and title, with its folders, e.g. "Architecture / Web / Authentication" |
| `search` | The full-text index | Up to 20 pages containing every word of the query, with a snippet and stale reasons. Titles weigh more than text |
| `read_page` | One page | Title, Markdown, owner, verified date, and stale reasons |
| `stale_pages` | Every page | The stale pages and their reasons |
| `propose_change` | The caller's write access | The pull request URL |

The search index is an SQLite FTS5 table in `ohara.db`, rebuilt on each sync and on startup, so search works even when GitHub is unreachable.

## Freshness

A page's freshness comes from its front matter (`owner`, `verified`, `covers`) and from code changes Ohara has recorded. See [Track freshness](../../documentation/docs-repository.md#track-freshness) for the rules.

When a repository other than the docs repository pushes to its default branch, the webhook:

1. lists the changed files with GitHub's compare API, using the installation token. For a new branch, it uses the files listed in the push's commits;
2. matches them against each page's `covers` patterns for that repository;
3. records a flag for each matching page, with the date, the changed files, and a compare link. Up to 20 flags are kept per page.

Each flag holds a hash of the page's file. When the page changes, the hash no longer matches and the flags are dropped.

## Propose changes

`propose_change` takes a title, a description, and the full Markdown of each page.

1. Ohara checks that the caller is signed in and can write to the docs repository.
2. It maps each path to a file: the existing file of that page, or a new `path.md`. Paths with empty parts or parts starting with `.` are refused.
3. It sets `verified` to today in each page's front matter, keeping the rest as written.
4. With the installation token, it creates a branch `ohara/<slug>-<random>` from the default branch, commits the files, and opens a pull request. The body ends with "Proposed through Ohara by @login".

If GitHub refuses with `403`, the app lacks write permissions. An admin grants **Contents** and **Pull requests** write permissions in the app settings, then accepts them on the installation.
