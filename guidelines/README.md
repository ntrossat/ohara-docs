---
verified: 2026-10-07
---

# Guidelines

The engineering rules every project follows. Coding assistants read them before planning a change, and `/ohara:review` checks a project against them.

| Page | What it covers |
|---|---|
| [Principles](principles.md) | What we build and how we decide |
| [Backend](backend.md) | Python and FastAPI |
| [Frontend](frontend.md) | React and TypeScript |
| [Security](security.md) | Access, tokens, secrets, and untrusted content |
| [Testing](testing.md) | What to test and how |
| [Git](git.md) | Branches, commits, pull requests, CI and CD |
| [Writing docs](writing-docs.md) | Where docs live, their format, and their voice |

Design rules live in [Design](../design/README.md): the brand, the style guide, and the UI kit.

## Using the guidelines

- Each rule is short and testable. "Always" and "never" mean exactly that.
- When code and a guideline disagree, fix the code, or propose a change to the guideline. Never leave both.
- Name the guidelines a change relies on in its pull request.
