# Webhook & Workflow Security Notes

Quick-reference answer for the question every client eventually asks: **"How do you secure a webhook from spam / abuse?"**

Built up during the sprint — refine as real client conversations surface new gaps.

---

## The four layers

### Layer 1 — Authenticate the sender (most important)

| Method | When to use | Notes |
|--------|-------------|-------|
| **HMAC signature** | Anything sensitive, or when sender supports it (Stripe, GitHub, Shopify, Slack all do) | Sender sends `X-Signature: sha256=<hash>` where `hash = HMAC(shared_secret, raw_body)`. n8n Code node recomputes and compares. Include a timestamp in the signed payload for replay protection. |
| **Bearer token / Header Auth** | B2B where you control both ends | n8n Webhook node has built-in Header Auth credential. Simpler than HMAC, fine for trusted partners. |
| **Secret in URL path** | Internal tools only | e.g. `/webhook/intake-a8f3b2c1`. Leaks in logs and proxy access logs. Avoid for anything sensitive. |

### Layer 2 — Limit the surface

- **IP allowlist:** if sender has fixed IPs, check `X-Forwarded-For` in a Code node and reject anything else. Useless on ngrok free (all traffic comes from ngrok's IPs); meaningful behind your own reverse proxy.
- **Origin / Referer check:** for browser-submitted forms.
- **CORS:** restrict which domains can submit from a browser.

### Layer 3 — Rate limit

- n8n Cloud has built-in rate limiting.
- **Self-hosted does not.** Rate limiting belongs at the reverse proxy (Caddy or Nginx) — wired up in Day 5's `docker-compose.yml`.
- Implement both **per-IP** and **per-API-key** limits — they catch different abuse patterns.

### Layer 4 — Validate the payload (table stakes)

- Required fields present.
- Format regex on emails, phone numbers, etc. (we did this in the Day 3 intake workflow).
- Reasonable size cap — reject payloads > N KB to stop someone POSTing a 50MB JSON.
- Type guards — don't be the workflow that 500s when a client sends `{"email": ["array", "of", "emails"]}`.

---

## Production gotchas

- **ngrok free tier:** IP allowlists are meaningless because traffic appears to come from ngrok's edge IPs. Layer 2 and Layer 3 only become real once you're behind your own proxy on a VPS.
- **n8n Webhook node "Respond: Immediately":** caller gets a 200 before validation runs. If you want true HTTP-level rejection, set the Webhook node to `Respond: Using 'Respond to Webhook' node` and put the Respond node on both branches of the If.
- **Logs:** workflow execution logs in n8n include the request body by default. If the body contains PII or secrets, scrub it (Set node early in the workflow that drops sensitive fields before any node that gets logged) or run n8n with `EXECUTIONS_DATA_SAVE_ON_SUCCESS=none`.

---

## Quick reference — what we've actually implemented in sprint workflows

| Workflow | Layer 1 | Layer 2 | Layer 3 | Layer 4 |
|----------|---------|---------|---------|---------|
| Day 3 — Form-to-Sheets intake | (none — public form) | (none — ngrok) | (none — n8n self-hosted) | ✅ regex + required fields + If validation gate |

Add Header Auth + scrub Layer 4 cap before pitching this workflow as a real deliverable to a client.
