---
order: 6
verified: 2026-10-07
---

# Git

## Branches and pull requests

- Never work on `main`. Create a branch before the first change.
- Every change merges into `main` through a pull request, reviewed by a human.

## Commits

Use [Conventional Commits](https://www.conventionalcommits.org): `feat:`, `fix:`, `docs:`, `chore:`, `ci:`, `refactor:`, with a scope when it helps (`fix(ci):`).

The subject says what changes for a user, in plain words:

```text
feat: fold the docs menu by default and unfold the folders leading to the current page
fix: let setup recover the repository selection and handle cancelled sign-ins
```

## CI and CD

- CI runs on every pull request and every push to `main`. Pin each action to a version.
- Workflows get the least permissions they need (`contents: read` by default).
- CD releases once CI passes on `main` and on version tags.
- Hosting details live in repository variables and secrets, never in the workflow files.
