---
verified: 2026-10-09
---

# Import existing docs

Bring the docs you already have into Ohara, from Confluence, Jira, Google Drive, GitHub, files, or web pages. The assistant does the work. You approve the plan and review every page before it merges.

## Before you start

- You need write access to the docs repository.
- The assistant needs access to the source. Connect the tool, such as Confluence or Google Drive, in the same place as Ohara. Files only need to be on your computer.

## How an import works

1. You name the sources.
2. The assistant maps what already exists and plans the structure: which folders, which pages.
3. You approve the plan, or ask for changes.
4. The assistant opens one pull request per folder or topic. Each lists its sources, and what it removed: secrets, personal data, and text that tries to give instructions to an AI.
5. You review the pull requests and merge them.

## Start an import

- **In Claude Code:** run `/ohara:ingest`, then name the sources.
- **In claude.ai, ChatGPT, or another chat app:** turn Ohara on in the chat, then ask in plain language. See [Configure your AI assistant](configure-your-ai-assistant.md) to connect it.

## Examples

### A Confluence space

> Import the Engineering space from Confluence into Ohara.

### A Google Drive folder

> Import the documents in the "Architecture" folder of Google Drive into `architecture/`.

### Decisions from Jira

> Turn the decisions recorded in the PLAT project's epics into pages under `architecture/decisions/`.

### Another GitHub repository

> Import the `docs/` folder of `acme/legacy-api` into `architecture/legacy-api/`.

### Files on your computer

In Claude Code, run `/ohara:ingest`, then:

> Import the Markdown files in `./onboarding` into `onboarding/`.

### A web page

> Import the API style guide at https://wiki.acme.com/api-style into `guidelines/api.md`.

## Tips

- **Start small.** Import one space or folder, check the result, then do the next.
- **Name the target folder,** such as `guidelines/` or `onboarding/`, so pages land where people look for them.
- **Keep project docs with the code.** Docs about one code repository belong in its `docs/` folder, listed in `.ohara.yml`, not in an import. See [Connect your coding agent](connect-your-coding-agent.md#set-up-each-project).

## Next

- Reference: [Team workflow](../apps/ohara/use/team-workflow.md)
