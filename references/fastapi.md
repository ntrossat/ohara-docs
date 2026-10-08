---
verified: 2026-10-08
---

# FastAPI user guide

Reference to the official FastAPI documentation. Our own rules for FastAPI code are in [Backend](../guidelines/backend.md).

Source: [Tutorial - User Guide](https://fastapi.tiangolo.com/tutorial/) and [Advanced User Guide](https://fastapi.tiangolo.com/advanced/).

## How the guides fit together

- The Tutorial covers the main features step by step. Each section builds on the one before, but you can jump to a single topic.
- The Tutorial alone is enough to build a complete application.
- The Advanced User Guide adds options, configuration and extra features. It assumes you read the Tutorial. Its topics are not always advanced: the answer to a common problem may be there.

## Set up and run

1. Install `uv`.
2. Create the project and add FastAPI:

```bash
uv init awesome-project --bare
cd awesome-project
uv add "fastapi[standard]"
```

3. Put the code in `main.py` and start the dev server:

```bash
uv run fastapi dev
```

The server runs at `http://127.0.0.1:8000`, with interactive docs at `/docs`. Use `fastapi run` in production.

| Install target | Includes |
| --- | --- |
| `fastapi[standard]` | Standard optional dependencies, including `fastapi-cloud-cli` |
| `fastapi[standard-no-fastapi-cloud-cli]` | Standard dependencies without the cloud CLI |
| `fastapi` | Core only |

`uv add` creates `.venv`, adds the dependency to `pyproject.toml` and writes `uv.lock`, so the same versions install elsewhere. With `pip`, create and activate a virtual environment, then `pip install "fastapi[standard]"`.

## Agent skill

FastAPI ships an official skill for coding agents, versioned with the installed package. Install it from the project with:

```bash
uvx library-skills
```

For Claude Code, choose `.claude/skills` as the install location.

## Advanced User Guide topics

| Area | Pages |
| --- | --- |
| Responses | [Stream data](https://fastapi.tiangolo.com/advanced/stream-data/), [Additional status codes](https://fastapi.tiangolo.com/advanced/additional-status-codes/), [Return a response directly](https://fastapi.tiangolo.com/advanced/response-directly/), [Custom response](https://fastapi.tiangolo.com/advanced/custom-response/), [Response cookies](https://fastapi.tiangolo.com/advanced/response-cookies/), [Response headers](https://fastapi.tiangolo.com/advanced/response-headers/), [Change status code](https://fastapi.tiangolo.com/advanced/response-change-status-code/) |
| OpenAPI | [Path operation advanced configuration](https://fastapi.tiangolo.com/advanced/path-operation-advanced-configuration/), [Additional responses](https://fastapi.tiangolo.com/advanced/additional-responses/), [Callbacks](https://fastapi.tiangolo.com/advanced/openapi-callbacks/), [Webhooks](https://fastapi.tiangolo.com/advanced/openapi-webhooks/), [Generating SDKs](https://fastapi.tiangolo.com/advanced/generate-clients/) |
| Requests and types | [Using the request directly](https://fastapi.tiangolo.com/advanced/using-request-directly/), [Dataclasses](https://fastapi.tiangolo.com/advanced/dataclasses/), [Advanced Python types](https://fastapi.tiangolo.com/advanced/advanced-python-types/), [JSON bytes as Base64](https://fastapi.tiangolo.com/advanced/json-base64-bytes/), [Strict content-type checking](https://fastapi.tiangolo.com/advanced/strict-content-type/) |
| Dependencies | [Advanced dependencies](https://fastapi.tiangolo.com/advanced/advanced-dependencies/) |
| Security | [Advanced security](https://fastapi.tiangolo.com/advanced/security/), [OAuth2 scopes](https://fastapi.tiangolo.com/advanced/security/oauth2-scopes/), [HTTP Basic auth](https://fastapi.tiangolo.com/advanced/security/http-basic-auth/) |
| App structure | [Advanced middleware](https://fastapi.tiangolo.com/advanced/middleware/), [Sub applications](https://fastapi.tiangolo.com/advanced/sub-applications/), [Behind a proxy](https://fastapi.tiangolo.com/advanced/behind-a-proxy/), [Templates](https://fastapi.tiangolo.com/advanced/templates/), [WebSockets](https://fastapi.tiangolo.com/advanced/websockets/), [Lifespan events](https://fastapi.tiangolo.com/advanced/events/), [Including WSGI](https://fastapi.tiangolo.com/advanced/wsgi/) |
| Configuration and observability | [Settings and environment variables](https://fastapi.tiangolo.com/advanced/settings/), [OpenTelemetry](https://fastapi.tiangolo.com/advanced/opentelemetry/) |
| Testing | [Testing WebSockets](https://fastapi.tiangolo.com/advanced/testing-websockets/), [Testing lifespan events](https://fastapi.tiangolo.com/advanced/testing-events/), [Dependency overrides](https://fastapi.tiangolo.com/advanced/testing-dependencies/), [Async tests](https://fastapi.tiangolo.com/advanced/async-tests/) |
