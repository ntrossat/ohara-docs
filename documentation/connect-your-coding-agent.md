---
verified: 2026-10-09
---

# Connect your coding agent

Coding agents reach Ohara through its MCP server, at `OHARA_URL/mcp`. Once connected, they search and read your docs and rules, and propose doc updates as pull requests.

## Connect Claude Code

```bash
claude mcp add --transport http ohara <OHARA_URL>/mcp
```

Any MCP client that supports the Streamable HTTP transport connects the same way, with the same URL.

## Sign in

For a private docs repository, the agent opens a GitHub sign-in in your browser on first use. Then:

1. Sign in with GitHub.
2. Click **Approve** on Ohara's page.

Approve only an agent you started yourself: a sign-in link from someone else would give them your access.

| You have on the docs repository | The agent can |
|---|---|
| Read access | Search and read the docs |
| Write access | Also propose changes, as pull requests a person reviews |

## CI and headless agents

Send a GitHub token instead of signing in:

```bash
claude mcp add --transport http ohara <OHARA_URL>/mcp --header "Authorization: Bearer <token>"
```

The token needs read access to the docs repository, and write access to propose changes.

## Set up each project

Open the project and run `/ohara:init`. The agent:

- checks that the GitHub App is installed on the project's repository, and opens its settings on GitHub when it isn't, so you can add it;
- offers to write a `.ohara.yml`, when the project keeps its own docs, so Ohara shows them;
- finds the rules and docs that apply to the project;
- adds the Ohara server to `.mcp.json`;
- writes an "Ohara instructions" section in `CLAUDE.md`, with those pages and the workflow;
- allows the read-only Ohara tools in `.claude/settings.json`, so only proposals ask for confirmation;
- offers to link the project's docs to the code, so Ohara flags them when the code changes.

Commit the changed files: everyone who works on the project gets the same setup. Running `/ohara:init` again is safe: it merges with existing files and replaces its own earlier setup.

For each change, the agent then reads the rules and docs that apply, writes the code, checks it against those rules, and proposes the doc updates in one pull request linked from the code pull request. A person reviews both and merges them.

## Commands

| Command | What it does |
|---|---|
| `/ohara:init` | Sets up the project, as above |
| `/ohara:update` | Compares the docs with the latest code changes, and proposes the updates |
| `/ohara:review` | Checks the project against the rules, and reports each violation with its rule, file, and line. Changes nothing unless asked |
| `/ohara:ingest` | Imports docs from other tools. See [Import existing docs](import-existing-docs.md) |

## Next

- [Configure your AI assistant](configure-your-ai-assistant.md) for claude.ai, ChatGPT, and other chat apps.
- Reference: [Coding assistants](../apps/ohara/use/coding-assistants.md), [Team workflow](../apps/ohara/use/team-workflow.md), [Access](../apps/ohara/configure/access.md)
