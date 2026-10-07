---
covers:
  - ntrossat/ohara:backend/ohara/codeowners.py
  - ntrossat/ohara:backend/ohara/main.py
  - ntrossat/ohara:backend/ohara/mcp_server.py
verified: 2026-10-07
---

# Merge docs with code

When a coding assistant updates the docs for a code change, Ohara can merge those docs together with the code, so routine updates don't pile up as pull requests. Folders the team guards, such as guidelines, still get a human review.

Docs that live in the code repository itself need none of this: they change in the code pull request, and Ohara syncs them once it merges. See [Sync docs from code repositories](docs-repository.md#sync-docs-from-code-repositories). This page covers the pages that live in the docs repository.

## How it works

A coding assistant proposes doc updates from a code branch, such as `feature/billing` in the `api` repository. Ohara sorts the pages with the docs repository's `CODEOWNERS` file:

| Pages | Pull request | Merged |
|---|---|---|
| With no code owner | `api/feature/billing` | Automatically, when the code pull request of `feature/billing` merges into the default branch |
| With a code owner | `api/feature/billing-review` | By a human, after review |

- When the code pull request is closed without merging, Ohara closes the automatic docs pull request.
- Until the code merges, the automatic pull request stays open: anyone can review it, edit it, or close it.
- Before merging, Ohara checks the pull request's files against `CODEOWNERS` again. If a file now has a code owner, or the merge fails, Ohara leaves a comment and a human merges it.
- The coding assistant puts the docs pull request links in the code pull request's description. Approving the code also approves the docs it brings.

Proposals that don't come from a code branch, such as imports with `/ohara:ingest`, always go to review.

## Choose the folders that need review

Add a `CODEOWNERS` file to the docs repository, in `.github/`, at the root, or in `docs/`. Each line is a pattern followed by the people or teams who review it:

```
# Guidelines and design need review
/guidelines/    @acme/architects
/design/        @acme/design
```

With this file, pages in `guidelines/` and `design/` go to review, and every other page merges with the code.

| Goal | `CODEOWNERS` |
|---|---|
| Review everything (the default) | No `CODEOWNERS` file, or `* @acme/docs` |
| Review some folders, merge the rest with the code | One line per guarded folder, as above |
| Review everything except one folder | `* @acme/docs` then `/architecture/` with no owner. The last matching line wins |

Adding a `CODEOWNERS` file turns on automatic merging for every folder it doesn't guard.

Patterns follow GitHub's rules: `/folder/` is a folder and everything in it, `folder/*` only its direct files, `*.svg` any file with that extension. Changes to `CODEOWNERS` apply once they are merged into the default branch.

On GitHub, code owners are also asked to review the pull requests that touch their folders. Branch protection that requires approvals on the docs repository's default branch also blocks Ohara's automatic merges: Ohara then leaves a comment and a human merges.

## Enable it

1. **Install the Ohara GitHub App on the code repositories.** In the app's installation settings, add each code repository, or run `/ohara:init` in the project, which opens those settings when the app is missing. This is the same step that flags stale pages, see [Add code repositories](docs-repository.md#add-code-repositories).
2. **Subscribe the app to pull request events.** Instances set up after this feature already are. For older ones, open the app's settings on GitHub, under **Permissions & events**, check **Pull request** in **Subscribe to events**, and save.
3. **Add a `CODEOWNERS` file** to the docs repository, as above.
4. **Run `/ohara:init` again** in each code project, so its assistant puts the docs pull request links in the code pull request's description.

Ohara merges with the app's own token, so a code project needs no extra setting. The app needs write access to contents and pull requests on the docs repository, which proposals already require.
