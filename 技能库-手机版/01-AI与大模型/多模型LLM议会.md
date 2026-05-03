# LLM Council

Query multiple AI models in parallel with a live web dashboard.

## Launch

cd ~/.claude/skills/llm-council/scripts
# Kill any existing server on the port
fuser -k 8787/tcp 2>/dev/null
# Start server
nohup python3 server.py > /tmp/council-server.log 2>&1 &
# Wait for startup, verify health
sleep 2 && curl -s --max-time 5 http://localhost:8787/health
# Export port for browser access
/app/export-port.sh 8787
```

Environment: `COUNCIL_PORT` (default 8787), `AI_GATEWAY_API_KEY` (required, auto-detected from environment).

## Files

## API

| Method | Path | Description |
|--------|------|-------------|
| GET | `/` | Dashboard |
| GET | `/api/models` | List models |
| GET | `/health` | Health check |
| POST | `/api/council/stream` | Query models (SSE) |
| POST | `/api/council/synthesize` | Synthesize consensus |
| POST | `/api/council/vote` | Anonymous model vote (SSE with keepalive) |

## Key Details

- Server uses `ThreadingHTTPServer` for concurrent request handling
- Vote endpoint streams SSE keepalive pings every 5s to prevent proxy timeouts (Cloudflare 524)
- Static files served with `Cache-Control: no-store, no-cache` headers
- Script tag uses cache-busting version param: `app.js?v=...` (bump when editing app.js)
- Model IDs use dots not hyphens: `anthropic/claude-sonnet-4.5` (not `claude-sonnet-4-5`)