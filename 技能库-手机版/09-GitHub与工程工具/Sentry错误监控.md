# Sentry (Read-only Observability)

## Quick start

- If not already authenticated, ask the user to run `sentry auth login` or set `SENTRY_AUTH_TOKEN` as an env var.
- Defaults: time range `24h`, environment `production`, limit 20.
- Always use `--json` when processing output programmatically. Use `--json --fields` to select specific fields and reduce output size.
- Use `sentry schema <resource>` to discover API endpoints quickly.

2. Authenticate: `sentry auth login`
3. Confirm authentication: `sentry auth status`
- Never ask the user to paste the full token in chat. Ask them to set it locally and confirm when ready.

## Core tasks (use Sentry CLI)

### 1) List issues (ordered by most recent)

sentry issue list \
  --query "is:unresolved environment:production" \
  --period 24h \
  --limit 20 \
  --json --fields shortId,title,priority,level,status
```

If auto-detection doesn't resolve org/project, pass them explicitly:
sentry issue list {your-org}/{your-project} \
  --query "is:unresolved environment:production" \
  --period 24h \
  --limit 20 \
  --json
```

### 2) Resolve an issue short ID to issue detail

sentry issue view {ABC-123} --json
```

Use the short ID format (e.g., `ABC-123`), not the numeric ID.

### 3) Issue detail

sentry issue view {ABC-123}
```

### 4) Issue events

sentry issue events {ABC-123} --limit 20 --json
```

### 5) Event detail

sentry event view {your-org}/{your-project}/{event_id} --json
```

### 6) AI-powered root cause analysis

sentry issue explain {ABC-123}
```

### 7) AI-powered fix plan

sentry issue plan {ABC-123}
```

## Fallback: arbitrary API access

sentry api /api/0/organizations/{your-org}/ --method GET
```

Use `sentry schema` to discover available API endpoints:
sentry schema issues
```

## Inputs and defaults

- `time_range`: default `24h` (pass as `--period 24h`).
- `environment`: default `prod` (pass as part of `--query`, e.g., `environment:production`).
- `limit`: default 20 (pass as `--limit`).
- `search_query`: optional `--query` parameter, uses Sentry search syntax (e.g., `is:unresolved`, `assigned:me`).
- `issue_short_id`: use directly with `sentry issue view`.

## Output formatting rules

- Issue list: show title, short_id, status, first_seen, last_seen, count, environments, top_tags; order by most recent.
- Event detail: include culprit, timestamp, environment, release, url.
- If no results, state explicitly.
- Redact PII in output (emails, IPs). Do not print raw stack traces.
- Never echo auth tokens.

## Golden test inputs

- Org: `{your-org}`
- Project: `{your-project}`
- Issue short ID: `{ABC-123}`

Example prompt: "List the top 10 open issues for prod in the last 24h."
Expected: ordered list with titles, short IDs, counts, last seen.