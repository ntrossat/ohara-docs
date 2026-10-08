---
verified: 2026-10-08
---

# Principles

## Product

A product should be:

1. **Beautiful and appealing.** It follows the [brand](../design/brand.md) and the [style guide](../design/style-guide.md) to the pixel.
2. **Focused on a few core features.** Say no to the rest. A feature that isn't on the roadmap waits.
3. **Simple and solid by design.** Fewer moving parts beat clever ones.

## AI keeps your docs up to date. You approve every change.

- Every change to code or docs goes through a pull request that a human approves.
- Never present AI output as final, in the product or in its copy.

## Simple by design

- Prefer the standard library and a few well-known dependencies. Add a dependency only when it removes real code.
- One way to do a thing. Reuse the existing helper before writing a new one.
- Small modules with one job each, named after that job.
- Keep state in one place. A service keeps nothing in process memory apart from caches, so a restart loses nothing.
- Fail safe: a background job that fails logs the error and leaves the last good state in place.

## Open source

- Never commit anything specific to one company, deployment, or person: no hosting provider, domain, credentials, or names of repositories other than the project's own. Make it configurable instead.
- Keep configuration minimal. A setting goes in the product's own setup page before it becomes an environment variable.
- Anyone can self-host, on any platform, with as few required variables as possible.
- New environment variables go in `.env.example`, with a comment that says what they do. Never commit `.env`.
