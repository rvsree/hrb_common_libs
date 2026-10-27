# hrb_common_libs

Shared connector + observability library for the HRB chatbot microservices split. Not a deployed service - no running instance exists anywhere, no "current state"/"target state"/"deployment" diagrams apply the way they do for the other 6 repos.

## For reviewers / new team members (5 minutes)

1. [Consumption architecture](../docs/architecture/diagrams/hrb_common_libs-consumption-architecture.drawio) - who depends on this and why a git-tag pin, not a floating branch or 10+ separate PyPI packages.
2. The extras table below - what each real consumer actually installs.
3. `ROADMAP.md` - all 17 build phases, if you want the full history.

## Install (as a consumer, not for local dev of this repo itself)

```bash
pip install "git+https://github.com/rvsree/hrb_common_libs.git@v0.4.0#egg=hrb_common_libs[db,cache]"
```

## Tech stack / extras

**Correction, 2026-10-24: 11 real extras, not 10.** This README previously listed 10 and omitted `vectorstore` - verified directly against `pyproject.toml`'s own `[project.optional-dependencies]` table:

| Extra | Pulls in |
|---|---|
| `observability` | `structlog`, `tenacity`, `boto3` |
| `db` | `psycopg[binary]` |
| `vectorstore` | `chromadb`, `pinecone`, `langchain-pinecone`, `langchain-chroma`, `langchain-openai` |
| `cache` | `redis` |
| `llm` | `openai`, `langchain-openai`, `anthropic`, `boto3` |
| `mcp` | `mcp`, `pydantic` (version-pinned to avoid a real conflict found in Phase 5) |
| `storage` | `boto3` |
| `web` | `tavily-python` |
| `auth` | `httpx`, `pyjwt` |
| `email` | `boto3` |
| `ci` | `PyGithub` |

(`dev` is a 12th group - `pytest`/`pytest-asyncio`/`respx`, test-only, not a product extra.) Every extra implicitly pulls in `observability` too - every real consumer gets structured logging and Secrets Manager access regardless of which extras it picks.

## Status, 2026-10-23: all 17 phases built, tagged v0.4.0, in real production use

Used by every other repo in this split (`hrb_api_gateway_service`, `hrb_platform_components`, `hrb_rag_ingestion_service`, `hrb_rag_retrieval_service`) - one git-tag-pinned version for everyone, never a floating dependency. Confirmed live: `Secrets Manager + request_id middleware` (Phase 17) is in real use by `hrb_rag_ingestion_service`'s own Lambda, confirmed working in production.

## Documentation index

| Doc | What it covers |
|---|---|
| [Overview / Spec / Architecture / Design / Decisions](../hrb_chatbot_service/docs/dev-reference/hrb_chatbot_v3/platform-and-admin/common-libs-connectors/) | The full HTML doc set |
| [Build + publish process](../hrb_chatbot_service/docs/dev-reference/hrb_chatbot_v3/deployment-and-ops/build-and-deploy/07-hrb_common_libs-build-and-publish.html) | Git-tag release model, step-by-step - no Docker, no App Runner, deliberately |
| [`platform-overview.html`](../hrb_chatbot_service/docs/dev-reference/hrb_chatbot_v3/architecture/platform-overview.html) | The cross-service hub - business capabilities, all 7 repos, one diagram |

Older paths under `docs/dev-reference/common-libs-connectors/` (no `hrb_chatbot_v3/` prefix) referenced in earlier commits are stale - that folder was reorganized 2026-10-23, and the links above reflect the real current location.
