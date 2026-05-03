# Progress Log — n8n AI Automation Sprint (Zero-Cost Edition)

**Sprint start date:** 2026-04-22
**Target launch date:** 2026-05-06
**Current day:** Day 4 (2026-05-04) — Days 1–3 complete

---

## How to use this file

Update at the end of each work session. Keep entries short. Re-upload to Claude Project when updated so Claude has current state.

Format per day:
- **Hours worked:** X
- **Completed:** what got done
- **Blocked on:** what's stuck
- **Tomorrow:** top 1–3 priorities
- **Notes:** anything Claude should know for context

---

## Day 0 — Pre-sprint setup (all free)

- [x] Claude Project created with all starter docs uploaded
- [x] Docker Desktop installed
- [x] ngrok account + auth token (free)
- [x] Ollama installed, Llama 3.2 pulled (free)
- [x] Google AI Studio account → Gemini API key (free tier)
- [x] Groq account → API key (free tier)
- [x] Telegram bot created via @BotFather (@n8n_AI_Automation_Sprint_bot)
- [x] GitHub account, new repo created: `n8n-sprint`
- [x] Google account ready (Sheets + OAuth testing)
- [x] Airtable free account
- [x] Loom free account
- [x] Calendar blocked for all 14 days

**Status:** done — completed 2026-04-22. All keys saved to `secrets.local.md`.

---

## Day 1 — n8n core + local environment ✅

- **Date:** 2026-04-26 → 2026-04-27
- **Hours worked:** 8 (split across sessions on 2026-04-26 and 2026-04-27)
- **Completed:**
  - n8n running locally via Docker (`docker run ... n8nio/n8n`), admin account created at `http://localhost:5678`
  - n8n Quickstart (docs.n8n.io/try-it-out/quickstart) walked end-to-end
  - **n8n Course Level 1 completed** — badge earned (covers editor UI, data structure, nodes/credentials, conditional logic, scheduling, import/export)
  - 3 practice workflows built and verified:
    - [x] `day1-schedule-branching` — Schedule → Set → If → 2 paths
    - [x] `day1-item-flow` — Manual → Code (3 items) → Set (proves once-per-item)
    - [x] `day1-expressions-routing` — Manual → Code (4 items) → If → Set on each branch
- **Blocked on:** none
- **Tomorrow:** Day 2 — Weather workflow (HTTP + transform + Telegram) with error branch; grab OpenWeather free API key
- **Notes:**
  - **Checkpoint — once-per-item vs once-per-execution (passed):** Connections between n8n nodes carry an array of items, and most nodes auto-loop their logic once per input item — so an HTTP/Set/If node downstream of an N-item upstream runs N times with `$json` scoped to one item each time. "Once per execution" mode (available on Code and a few others) runs the node a single time with the whole array via `$input.all()`, which is what you need for aggregations or batched API calls. Picking wrong is the #1 way to either blow API budget (N calls when 1 was intended) or break aggregation logic.

---

## Day 2 — Data transformation + HTTP + error handling ✅

- **Date:** 2026-04-30
- **Hours worked:** 4
- **Completed:**
  - n8n "Data transformation" + Expressions + Code node docs read end-to-end
  - **`day2-weather` workflow built and activated:** Schedule (every 6h) → HTTP Request (Open-Meteo current weather, Cairo) → Code node (flatten nested `current.*` into a flat object) → Telegram (DM via `@n8n_AI_Automation_Sprint_bot`)
  - **Error branch wired:** HTTP node Settings → On Error = `Continue (using error output)` → second Telegram node alerts on failure. Verified by temporarily pointing URL at `/v1/nope` and confirming alert fired
  - Workflow saved, activated, schedule confirmed firing in Executions tab
  - **30-min REST API checkpoint passed:** rebuilt the same shape against Frankfurter (`api.frankfurter.dev/v1/latest?base=USD`) — no signup, ECB rates
  - **Soft-error If node added** to the Frankfurter flow: API returns 200 OK with `success: false` on bad input, which the HTTP error branch wouldn't catch. If node on `{{ $json.success }} === true` routes to error Telegram on false. Real-world pattern locked in
- **Blocked on:** none (resolved mid-day — see notes)
- **Tomorrow:** Day 3 — install ngrok, set `WEBHOOK_URL` env, build Form-to-Sheets intake (webhook → validate → Google Sheets → Telegram), configure Google Sheets OAuth2
- **Notes:**
  - **OpenWeather inaccessible from Egypt:** marketing site `openweathermap.org` returns `ERR_CONNECTION_REFUSED` on both home WiFi and mobile data (likely ISP-level DNS block of that specific domain — `api.openweathermap.org` itself was reachable and returned a clean 401). Pivoted to Open-Meteo (keyless) instead of burning time on a VPN signup. No Day 2 skill lost — same HTTP/transform/error patterns practiced
  - **Telegram chat ID gotcha:** initially pasted the full `https://api.telegram.org/bot<TOKEN>/...` URL into the Chat ID field and the node silently returned Telegram's HTML landing page. Chat ID must be the numeric ID (from `getUpdates` after DMing the bot once)
  - **Bot token leak + rotation:** original token was visible in a screenshot during debugging. Revoked via BotFather `/revoke`, new token updated in n8n credential and `secrets.local.md`
  - **exchangerate.host now requires an access key** — switched checkpoint API to Frankfurter
  - **Lesson — letter vs spirit of the deliverable:** plan asked for an HTTP-level error branch (letter); real APIs return 200 + `success:false` payloads (spirit). The soft-error If node is the part that separates $40/hr from $100/hr work

---

## Day 3 — Webhooks + credentials + ngrok ✅

- **Date:** 2026-04-30
- **Hours worked:** 8
- **Completed:**
  - ngrok installed and authenticated; agent updated from 3.3.1 → 3.39.1 to clear `ERR_NGROK_121` (free tier minimum agent version)
  - Public tunnel up: `https://provable-credibly-june.ngrok-free.dev` → `localhost:5678`
  - n8n restarted with `WEBHOOK_URL` so Webhook nodes advertise the public URL; smoke test passed (curl → 200, execution captured payload in 38ms)
  - Google Cloud project `n8n-sprint` created; Sheets API + Drive API enabled; OAuth consent screen wired (External, self as test user); OAuth Web client created with `http://localhost:5678/rest/oauth2-credential/callback` redirect
  - Google Sheets OAuth2 credential connected in n8n; full Sheets + Drive scopes granted
  - **`day3-intake-form` workflow built and activated:** Webhook (POST `/intake`, "Respond: Using 'Respond to Webhook' node") → If (email regex + required fields) → [TRUE] Google Sheets append → Telegram confirm → Respond (200, `{"ok":true}`) / [FALSE] Telegram alert → Respond (400, `{"error":"invalid payload"}`)
  - Target sheet `n8n-sprint - intake` created with `timestamp | name | email | message` headers
  - End-to-end test passed: valid POST → 200 + sheet row + Telegram ping; invalid POST (missing email) → 400 + Telegram alert + sheet untouched
  - **Security checkpoint passed:** four-layer model written up in `docs/security-notes.md` (HMAC/bearer/path-secret auth, IP allowlist + Origin/CORS, rate-limit-at-proxy, payload validation). Day 3 workflow currently only implements Layer 4 — that's flagged in the doc
  - Helper script `start-n8n.bat` saved at sprint root: prompts for ngrok URL, runs the docker command with the right env vars
- **Blocked on:** none (resolved mid-day — see notes)
- **Tomorrow:** Day 4 — install Ollama, pull `llama3.2` + `nomic-embed-text`, build email classifier (Ollama vs Gemini Flash vs Groq comparison), force JSON schema with structured output parser
- **Notes:**
  - **ngrok agent version (`ERR_NGROK_121`):** free tier requires agent ≥ 3.20.0; installed v3.3.1 was rejected. Fixed by reinstalling fresh from ngrok.com. Authtoken persists, no re-auth needed
  - **OAuth redirect URL flipped to ngrok host:** initially set `N8N_HOST` + `N8N_PROTOCOL` env vars to make the editor reachable through ngrok — side effect was that the OAuth Redirect URL in the Sheets credential modal showed the ngrok host, which would have meant updating Google Console every time the ngrok URL rotated. Removing those vars wasn't enough — `WEBHOOK_URL` alone in n8n 1.x is treated as the editor's public base URL too
  - **Real fix — split the URLs:** added `N8N_EDITOR_BASE_URL=http://localhost:5678/` alongside `WEBHOOK_URL=<ngrok>`. Editor + OAuth callbacks pinned to localhost (registered once in Google Console, never rotates), webhook nodes still advertise ngrok publicly. Survives ngrok URL changes for the rest of the sprint with zero rework. **This is the pattern to remember for any local-dev OAuth on n8n + ngrok**
  - **OAuth lesson — auth happens once, tokens live forever:** even if redirect URI mismatches in future, the saved access/refresh tokens keep working for ongoing API calls. Redirect URI only matters at the moment of clicking "Sign in with Google". Useful escape hatch if Google Console gets out of sync
  - **Google Cloud Console UI changed** — what used to be "OAuth consent screen" + "Credentials → OAuth client ID" is now "Google Auth Platform" wizard + "Clients". Same outcome, new flow. Documented for future me
  - **Webhook node "Respond: Immediately" can't return custom status codes** — caller gets the configured Response Code (default 200) before any downstream nodes run. To return a real 400 on validation failure, switch the Webhook node to "Respond: Using 'Respond to Webhook' node" and put a Respond node on every terminal branch (TRUE and FALSE) — otherwise the caller hangs
  - **Lesson — letter vs spirit, again:** plan said "validate input." Letter = If node with regex (which we built). Spirit = the four-layer security model that a real client expects to hear. The write-up in `security-notes.md` is the part that turns this into a deliverable I can sell, not just a workflow that runs

---

## Day 4 — Ollama + free LLMs + prompt engineering

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**
- **Model that worked best for classification:**

---

## Day 5 — Production thinking + DevOps (local only)

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 6 — Flagship #1 build: Lead Enrichment Pipeline

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 7 — Flagship #1 polish + documentation

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

### Week 1 retro

- Biggest win:
- Biggest blocker:
- Adjustments for Week 2:

---

## Day 8 — Flagship #2 design + RAG with free embeddings

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 9 — Flagship #2 build: Support Triage Agent

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 10 — Flagship #3 build: Document Processing

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 11 — Polish all three + monitoring

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 12 — Portfolio site + positioning

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**
- **Portfolio URL:**

---

## Day 13 — Service packages + outreach assets

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 14 — Launch day

- **Hours worked:**
- **VPS decision (stayed on ngrok / spun up $6 VPS):**
- **Completed:**
- **Blocked on:**
- **Notes:**

### Sprint retro

- What shipped:
- What slipped:
- First outreach response by:
- First contract signed by:
- Rate achieved:

---

## Post-sprint tracking (Days 15–30)

| Day | Proposals sent | Responses | Calls booked | Contracts signed | Revenue |
|-----|---------------|-----------|--------------|------------------|---------|
| 15  |               |           |              |                  |         |
| 16  |               |           |              |                  |         |
| 17  |               |           |              |                  |         |
| ... |               |           |              |                  |         |

---

## Running notes / ideas to revisit

- _[Drop one-liners here mid-flight]_
