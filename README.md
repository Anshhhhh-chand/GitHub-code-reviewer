# GitHub PR Reviewer (n8n)

Small n8n setup that reviews GitHub PRs with an LLM and labels them.

## Run

1) Start n8n
```bash
docker compose up -d
```
Open http://localhost:5678

2) Import the workflow
- In the editor, menu (≡) → "Import from file" → pick `My workflow 2.example.json` (sanitized).
- Set credentials in the workflow (GitHub OAuth/App, LLM key).
- The file `My workflow 2.json` may contain personal values. Prefer the `.example.json` when sharing.

## Cloudflare tunnel (to receive webhooks publicly)

When you need a public URL (e.g., GitHub → n8n), create a temporary tunnel:

```bash
# Install: https://developers.cloudflare.com/cloudflare-one/connections/connect-networks/downloads/
cloudflared tunnel --url http://localhost:5678
```

Copy the generated URL, e.g. `https://abc123.trycloudflare.com`.

Tell n8n to use it for webhooks by setting `WEBHOOK_URL` (so your Webhook node URLs are public):

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
- Keep `N8N_HOST=localhost` for local. `WEBHOOK_URL` overrides only the external URLs used in nodes.
- If you later deploy with your own domain + HTTPS, set `N8N_HOST=<domain>` and `N8N_PROTOCOL=https`.



## Use the example workflow

1) Import `My workflow 2.example.json`.
2) Edit placeholders in nodes:
   - owner: `<your-github-username>`
   - repository: `<your-repo>`
3) Set credentials inside n8n:
   - GitHub (OAuth/App)
   - LLM provider key (for the Groq/OpenAI node you use)
4) If testing via GitHub events from outside your machine, set a tunnel URL:
   - Add `WEBHOOK_URL=https://<your-tunnel>.trycloudflare.com` in `docker-compose.yml` and restart.
5) Activate the workflow and open a test Pull Request in `<your-repo>`.
6) Check n8n Executions for results and GitHub for comments/labels.
