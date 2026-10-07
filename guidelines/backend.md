---
verified: 2026-10-07
---


# Backend

Python 3.14 with FastAPI, managed with `uv`.

## Structure

- One package, one module per concern. The entry module holds the routes and wires the modules together. Other modules never import it.
- Each module starts with a docstring that says what it does and what it owns.
- A function's docstring explains why or a rule it follows, not what the code already says.
- Read configuration in one module only. Never read `os.environ` elsewhere.
- Group routes under comment headings, in the order a user meets them.

## Style

- Type hints on every function signature. Use built-in generics and `X | None`.
- Name things in plain words: `can_read`, `require_admin`.
- Constants in `UPPER_CASE` at the top of the module, with units in the name or a comment (`CHECK_INTERVAL = 300  # seconds`).
- Lines up to about 140 characters. Match the surrounding code.

## State

- Keep state in one place, behind one module. No other code touches storage directly.
- Give temporary data an expiry, so it cleans itself up.
- Write related changes together, in one transaction.
- Consume single-use data atomically, so only one caller gets it.
- Migrations run on start, are idempotent, and never drop old data before the new data is saved.

## Requests and errors

- Guard routes with dependencies (`Depends(...)`), not checks at the top of each handler.
- Raise `HTTPException` with the right status and a message that says what to do.
- Validate request bodies with Pydantic models.
- Run slow blocking work with `asyncio.to_thread`: subprocesses, synchronous network calls, and work on many files. Short SQLite queries and single-file reads run inline.
- Background work logs its exceptions and never crashes the server.
- Never return a stack trace to a client. Errors reach it as a readable message.

## External APIs

- Each external API lives in its own module, through one client with a timeout.
- Turn an expired token into a dedicated exception and handle it where the session lives.
- Keep only the fields you need from an API response.
