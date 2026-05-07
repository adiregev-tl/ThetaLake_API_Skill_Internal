---
name: theta-lake-api
description: Use when helping with Theta Lake API requests, including finding endpoints, composing authenticated curl calls, explaining request/response schemas, or safely executing read/write API operations.
---

# Theta Lake API

Use this skill when the user asks to work with the Theta Lake API. Help them map natural language requests to the right API endpoints, compose calls, interpret responses, and safely execute requests when appropriate.

## Credentials

Credentials are loaded by `scripts/tl-curl.sh` from `.env.theta-lake` in this skill directory. The file should define:

- `TL_BASE_URL`
- Either `TL_API_TOKEN`
- Or `TL_CLIENT_ID`, `TL_CLIENT_SECRET`, and `TL_TOKEN_URL`

Never print full tokens or secrets. Mask tokens as `****...last4` if they must be referenced.

## API Reference

Start with `api-quick-ref.md` for endpoint discovery.

For detailed parameters, use the relevant reference:

- Search: `search-api.md`
- Auth and token context: `auth-guide.md`
- Cases: `cases-api.md`
- Identities: `identities-api.md`
- Ingestion and upload: `ingestion-api.md`
- Legal hold: `legal-hold-api.md`
- Records: `records-api.md`
- Supervision spaces: `supervision-spaces-api.md`
- Users and groups: `users-groups-api.md`
- Workflows, queues, and record actions: `workflows-api.md`
- Workspaces: `workspaces-api.md`
- Other endpoints: `other-endpoints-api.md`
- Shared pagination, errors, and date patterns: `common-patterns.md`

For exact schemas, inspect `theta_lake_api_v1.yml` or `theta_lake_api_v1_23.yml`.

## Workflow

1. Parse the user's intent and map it to candidate endpoint operations.
2. Read the quick reference, then the detailed reference file for the selected domain.
3. Compose commands using the wrapper:

```bash
./scripts/tl-curl.sh METHOD /endpoint [extra-curl-args]
```

Examples:

```bash
./scripts/tl-curl.sh GET /token/context
./scripts/tl-curl.sh GET "/cases?status=open&max=50"
./scripts/tl-curl.sh POST /search/records -d '{"range":{"days":7},"risk":["high"]}'
```

4. For `GET` requests, show the command, execute it when useful, and summarize the response.
5. For `POST`, `PUT`, `PATCH`, or `DELETE`, show the command and ask for confirmation before execution.
6. Format list responses as compact tables when useful. Mention pagination if more pages are available.
7. On errors, explain the likely cause, permission requirement, or configuration fix.

## Safety Rules

- Confirm before destructive operations or state-changing writes.
- Do not store raw responses containing PII; summarize instead.
- Warn about heavy rate limits, especially content searches.
- If a `403` is returned, explain the likely missing permission from the endpoint reference.
- If the request is ambiguous, list the likely endpoint candidates and ask for the minimum needed clarification.
