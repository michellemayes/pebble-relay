# pebble-relay

Cloudflare Worker (`pebble`) that receives Pebble Index voice-note webhooks, classifies each note as
`personal` or `work` with Workers AI (Typesafe Jev), and fires the matching Claude Code routine.

## Deploy

Pushes to `main` deploy automatically via `.github/workflows/deploy.yml`.

One-time setup, in GitHub → Settings → Secrets and variables → Actions:

- `CLOUDFLARE_API_TOKEN` – API token with the "Edit Cloudflare Workers" template
- `CLOUDFLARE_ACCOUNT_ID` – your Cloudflare account ID

Manual deploy: `npm install && npx wrangler deploy`.

## Worker secrets (set in Cloudflare, never committed)

| Name | Purpose |
|------|---------|
| `RELAY_SECRET` | Bearer token Pebble sends |
| `PERSONAL_ROUTINE_URL` / `PERSONAL_ROUTINE_TOKEN` | Fire endpoint and token for the personal routine |
| `WORK_ROUTINE_URL` / `WORK_ROUTINE_TOKEN` | Fire endpoint and token for the work routine |

Set or rotate with `npx wrangler secret put <NAME>`. `keep_vars = true` in `wrangler.toml` stops deploys
from wiping secrets and variables set in the dashboard.

Optional plain variable: `CLASSIFIER_MODEL` (defaults to `typesafe/jev-1.14.0`).

## Local dev

`npx wrangler dev`, with secrets in a gitignored `.dev.vars` file.
