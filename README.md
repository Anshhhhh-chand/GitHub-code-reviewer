# GitHub PR Reviewer (n8n)

Small n8n setup that reviews GitHub PRs with an LLM and labels them.

## Run

1) Start n8n
```bash
docker compose up -d
```
Editor: http://localhost:5678

2) Import the workflow
- Editor menu (≡) → "Import from file" → select `My workflow 2.example.json` .
- Configure credentials in the workflow (GitHub OAuth/App, LLM key).
- 

## Cloudflare tunnel (public webhooks)

For external webhooks, create a temporary Cloudflare Tunnel:

```bash
# Install: https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/
cloudflared tunnel --url http://localhost:5678
```

Copy the generated URL, e.g., `https://abc123.trycloudflare.com`.

Set `WEBHOOK_URL` to the tunnel URL (used for webhook endpoints):

```yaml
# docker-compose.yml → services.n8n.environment
- WEBHOOK_URL=https://abc123.trycloudflare.com
# Optional, only if you want the editor to load via the tunnel as well:
# - N8N_EDITOR_BASE_URL=https://abc123.trycloudflare.com
```

Then restart:
```bash
docker compose up -d
```

Notes:
- Keep `N8N_HOST=localhost` for local. `WEBHOOK_URL` overrides only external URLs used in nodes.
- For production with a domain + HTTPS, set `N8N_HOST=<domain>` and `N8N_PROTOCOL=https`.



## Use the example workflow

1) Import `My workflow 2.example.json`.
2) Update placeholders in nodes:
   - owner: `<your-github-username>`
   - repository: `<your-repo>`
3) Configure credentials in n8n:
   - GitHub (OAuth/App)
   - LLM provider key (for the Groq/OpenAI node you use)
4) For external testing, set a tunnel URL:
   - Add `WEBHOOK_URL=https://<your-tunnel>.trycloudflare.com` in `docker-compose.yml` and restart.
5) Activate the workflow and open a test Pull Request in `<your-repo>`.
6) Verify results in n8n Executions and in PR comments/labels.
