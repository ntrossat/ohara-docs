---
order: 5
verified: 2026-10-07
---

# Testing

## What to test

- Test behavior through the public surface: HTTP routes, MCP tools, and module functions other modules call.
- Every rule in the product docs has a test.
- A bug fix starts with a test that fails without it.

## How

- Backend tests use `pytest` and run with `uv run pytest`.
- Name a test after the behavior it proves: `test_private_repository_requires_sign_in`.
- Each test gets its own data directory (an autouse fixture). Never share state between tests.
- Use fixtures for setup that repeats, such as a signed-in user.
- Mock external APIs (`respx` for HTTP). Tests never call the network.
- Use fake, generic names in tests: `acme/handbook`, `docs.example.com`, `ada`.

## In CI

- CI runs the backend tests and the frontend build on every pull request and every push to `main`.
- A pull request merges only when CI passes.
