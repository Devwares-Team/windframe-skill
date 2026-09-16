# Windframe HTTP API

Read this when making requests or diagnosing failures. Use direct HTTP; no MCP installation, resources, OAuth, or token-refresh flow is required.

## Contract

Base URL: `https://mcp.windframe.dev/api`.

Authenticate with `Authorization: Bearer` populated from `WINDFRAME_API_KEY` inside the HTTP client. `x-api-key` is also supported; use one header, not both.

- `GET /ui-styles` returns `{ "styles": ["..."] }`. Use live values and any returned metadata.
- `POST /design-context` requires nonempty string fields `prompt`, `uiStyle`, and `primaryColor`. Send JSON with `Content-Type: application/json`.
- Design context is returned inline as a string, with optional `fonts` of unspecified shape. Apply it to project code. A success status without usable context is incomplete, not successful UI generation.

## Request examples

These examples describe the HTTP format. Read `WINDFRAME_API_KEY` from the environment inside the available HTTP client and use its value in the header. Do not put the expanded key in tool arguments or logs, enable verbose HTTP tracing, or forward credentials across redirects.

### Fetch styles and validate access

```http
GET https://mcp.windframe.dev/api/ui-styles
Authorization: Bearer <WINDFRAME_API_KEY>
Accept: application/json
```

Response:

```json
{
  "styles": ["..."]
}
```

Use actual style values from the response. Present recommendations only for choices the user has not already supplied.

### Fetch design context

After the user chooses a live style and primary color:

```http
POST https://mcp.windframe.dev/api/design-context
Authorization: Bearer <WINDFRAME_API_KEY>
Content-Type: application/json

{
  "prompt": "Specific UI brief with relevant project constraints",
  "uiStyle": "User-selected live style",
  "primaryColor": "User-selected color"
}
```

Response:

```json
{
  "context": "Inline design guidance"
}
```

The response may also include `fonts`. Send only relevant project context, excluding secrets. Apply the returned guidance and fonts in the project; do not present the response as finished UI.

## Errors and recovery

| Evidence | Next action |
| --- | --- |
| Local missing key or 401 `missing_api_key` | Follow [authentication.md](authentication.md); check environment inheritance/header setup. |
| 401 `invalid_api_key` | Have the user verify or replace the locally configured key. Do not ask for it in chat. |
| 403 `pro_plan_required` | Explain the plan restriction and link [Windframe pricing](https://windframe.dev/pricing). Do not retry unchanged. |
| Network, DNS, TLS, timeout | Report connectivity failure, not invalid credentials. |
| 502 `api_key_validation_unavailable` | Validation service failure; do not rotate a key solely for this error. |
| 502 `ui_styles_unavailable` or `design_context_unavailable` | Report the Windframe service failure. |
| 400 `missing_required_body_fields` | Correct missing/empty prompt, style, or color fields, then retry. |
| Explicit rejected style/color | Ask for a valid alternative; never silently substitute. |
| Other status, redirect, or malformed success | Report the safe status/classification and uncertainty; do not invent an auth diagnosis. |

Retry transient failures at most once, retaining the task and selections. Avoid an automatic POST retry after an ambiguous timeout because it may already have run. After setup or recovery, validate with styles and resume.

The current middleware maps every non-OK key-validator response to `invalid_api_key`; a validator outage can therefore appear as 401. A previously working key suddenly failing does not prove revocation.

## Compatibility limits

The API requires the validated plan value `pro`; do not promise Team eligibility without confirmation from Windframe. Color handling follows [styles.md](styles.md).
