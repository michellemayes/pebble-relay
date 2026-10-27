# pebble-relay

Cloudflare Worker (`pebble`) that receives Pebble Index voice-note webhooks, classifies each note as
`personal` or `work` with Workers AI (Typesafe Jev), and fires the matching Claude Code routine.

## Deploy

Pushes to `main` deploy through Cloudflare Workers Builds, so no Cloudflare token is stored in GitHub.

One-time setup: in Cloudflare, open the `pebble` Worker → Settings → Builds → Connect, pick this repo,
and keep the branch `main` and the deploy command `npx wrangler deploy`. The `name` in `wrangler.toml`
must stay `pebble` to match the Worker.

Treat `wrangler.toml` and `src/` as the source of truth. A code change made in the dashboard is overwritten
by the next push, so make changes here instead.

Manual deploy: `npm install && npx wrangler deploy`.

## Worker secrets (set in Cloudflare, never committed)

| Name | Purpose |
|------|---------|
| `RELAY_SECRET` | Bearer token Pebble sends |
| `PERSONAL_ROUTINE_URL` / `PERSONAL_ROUTINE_TOKEN` | Fire endpoint and token for the personal routine |
| `WORK_ROUTINE_URL` / `WORK_ROUTINE_TOKEN` | Fire endpoint and token for the work routine |

Set or rotate with `npx wrangler secret put <NAME>`. `keep_vars = true` in `wrangler.toml` stops deploys
from wiping secrets and variables set in the dashboard.

Optional plain variables (Settings → Variables and Secrets, type "Text"; kept across deploys):

- `CLASSIFIER_MODEL` – defaults to `typesafe/jev-1.14.0`
- `PERSONAL_PROJECTS` – comma-separated side project names the classifier should file as personal

## Local dev

`npx wrangler dev`, with secrets in a gitignored `.dev.vars` file.
