# Progress Log — n8n AI Automation Sprint (Zero-Cost Edition)

**Sprint start date:** 2026-04-22
**Target launch date:** 2026-05-06
**Current day:** Day 2 (2026-04-27) — Day 1 complete

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

## Day 2 — Data transformation + HTTP + error handling

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

---

## Day 3 — Webhooks + credentials + ngrok

- **Hours worked:**
- **Completed:**
- **Blocked on:**
- **Tomorrow:**
- **Notes:**

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
