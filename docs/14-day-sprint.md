# 14-Day Sprint: AI Workflow Automation Freelancer (n8n + LLM) — Zero-Cost Edition

**Goal:** Portfolio-credible AI automation consultant ready to pitch clients at $60–120/hr by end of Day 14.

**Budget:** 80–160 hrs total (20–40 hrs/week × 2 weeks). ~8 hrs/day weekdays + optional catch-up weekends.

**Cost to execute:** $0 during learning. Pay only on Day 14 (~$6 VPS) or when a buyer is ready.

**Three flagship deliverables:**
1. AI Lead Enrichment Pipeline
2. AI Customer Support Triage Agent (with RAG)
3. AI Document Processing Workflow

---

## The zero-cost stack

| Need | Paid option (skip for now) | Free substitute |
|------|---------------------------|-----------------|
| n8n hosting | VPS $6/mo | Docker Desktop on laptop |
| Public webhook URL | VPS + domain | `ngrok` free tier |
| LLM API | OpenAI / Anthropic ($) | **Ollama (local, 100% free)** + Gemini API (free tier) + Groq API (free tier) |
| Vector DB | Pinecone paid | pgvector on local Postgres OR Supabase free tier |
| Embeddings | OpenAI embeddings | Ollama `nomic-embed-text` (local) OR Gemini embeddings (free) |
| OCR | Google Document AI ($) | Tesseract (local) OR Gemini Vision (free tier) |
| CRM test target | HubSpot paid | HubSpot free tier, Airtable free tier, or Google Sheets |
| Monitoring | Paid observability | Telegram bot (free) + n8n execution logs |
| Portfolio site | Framer Pro | Carrd free tier or GitHub Pages (free) |
| Video demos | Loom paid | Loom free (25 videos, 5 min each) |

**Ollama RAM requirements:** 8GB+ for small models (Llama 3.2 3B, Qwen 2.5), 16GB+ for better ones. If your laptop can't run it, use Gemini + Groq free tiers exclusively.

---

## Week 1 — Foundations + First Flagship

### Day 1 — n8n core + local environment (~6–8 hrs)

**Objectives:** Workflows, nodes, triggers, n8n expression language, item-based data flow.

**Actions:**
- Install Docker Desktop
- Run n8n locally: `docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n n8nio/n8n`
- Open `http://localhost:5678`, create local admin account
- Complete n8n's Quickstart in docs.n8n.io
- Watch n8n Academy "Level 1: Beginner" (free)
- Throwaway workflow: Schedule trigger → Set node → If node → 2 paths

**Deliverable:** Working local n8n + 3 practice workflows.

**Checkpoint:** Explain "once per item" vs "once per execution". If unclear, rewatch Academy Level 1.

---

### Day 2 — Data transformation + HTTP + error handling (~8 hrs)

**Objectives:** JSON manipulation, Code node (JS), HTTP Request node (auth, pagination), error patterns.

**Actions:**
- Follow n8n docs "Data transformation" end-to-end
- Build **Weather workflow**: schedule → HTTP (OpenWeather free API) → transform → Telegram message (create free bot via @BotFather)
- Add error branch: API failure → alert
- Code node: write JS to flatten nested API response

**Deliverable:** Weather workflow running on schedule with error handling.

**Checkpoint:** Can you wire up any REST API in <30 min by reading its docs?

---

### Day 3 — Webhooks + credentials + ngrok (~8 hrs)

**Objectives:** Webhook triggers, OAuth2, exposing local webhooks via ngrok.

**Actions:**
- Install ngrok (free), create account, get auth token
- Run `ngrok http 5678` → copy public HTTPS URL
- Set n8n env var: `WEBHOOK_URL=https://your-ngrok-url.ngrok.io/`
- Build **Form-to-Sheets intake**: webhook → validate → Google Sheets → Telegram confirmation
- Test webhook with Postman or curl
- Configure OAuth2 for Google Sheets

**Deliverable:** Public webhook (via ngrok) processing form submissions end-to-end.

**Checkpoint:** How would you secure a webhook from spam? (HMAC, rate limits, allowed origins.)

**Ngrok reality:** Free URLs change every restart. Fine for learning/demos, not production — that's why we move to VPS on launch day.

---

### Day 4 — Ollama + free LLMs + prompt engineering (~8 hrs)

**Objectives:** Local LLMs with Ollama, n8n AI Agent node, structured JSON output.

**Actions:**
- Install Ollama from `ollama.com`
- Pull models: `ollama pull llama3.2` (3B, fast), `ollama pull qwen2.5:7b` (better if RAM allows), `ollama pull nomic-embed-text` (for embeddings)
- Test: `ollama run llama3.2` in terminal
- In n8n: use HTTP Request node → `http://host.docker.internal:11434/api/generate` (Docker → host) OR install community Ollama node
- Also grab free API keys: Google AI Studio (Gemini) + Groq
- Build **AI email classifier**: manual trigger → sample email → Ollama classifies as [urgent/sales/support/spam] → route
- Compare: Ollama Llama 3.2 vs Gemini Flash vs Groq Llama 3.3 70B on same prompts
- Force JSON schema with structured output parser

**Deliverable:** Classifier with >90% accuracy using at least one local + one cloud free model.

**When to use which model:**
- **Ollama:** privacy-sensitive, unlimited runs, slower on consumer hardware
- **Gemini Flash:** fast, capable, rate-limited
- **Groq:** blazing fast demos, rate-limited

---

### Day 5 — Production thinking + DevOps fundamentals (~8–10 hrs)

**No VPS spend — but do the full DevOps work.** This is your moat day.

**Objectives:** Docker Compose, reverse proxy + SSL concepts, queue mode, backups.

**Actions:**
- Build a proper `docker-compose.yml` locally with:
  - n8n in queue mode
  - Postgres (replacing SQLite)
  - Redis for the queue
  - One worker container
- Write Caddy config for SSL (don't run it yet — keep ready for Day 14)
- Write Postgres backup script (cron + local rsync for now)
- Write a deployment README as if deploying to a VPS tomorrow
- Commit everything to `infra/` in your GitHub repo

**Deliverable:** Complete runnable `docker-compose.yml` + deployment README — itself a portfolio artifact.

**Checkpoint:** If a client asks "can you self-host n8n on our infra?", you now say yes with a plan — without having paid for a VPS.

---

### Day 6 — Flagship #1 build: Lead Enrichment Pipeline (~8–10 hrs)

**Architecture:** Webhook (ngrok) → company domain → free enrichment (Hunter.io / Abstract API free tiers, or public scraping) → Ollama or Gemini scoring → Airtable / Google Sheets → Telegram alert for hot leads.

**Actions:**
- Sketch architecture (Excalidraw)
- Build happy path end-to-end
- Add dedup: check Airtable before re-enriching
- Add error handling with exponential backoff retries
- Add scoring rubric in the prompt (0-100 + reasoning)

**Deliverable:** Pipeline processing 5 test leads with full execution trace.

**Checkpoint:** Does it survive 100 leads at once? (Use batching + rate limiting.)

**Free-tier gotcha:** Enrichment APIs cap at 25–100 calls/month free. Design around it — batch, cache.

---

### Day 7 — Flagship #1 polish + documentation (~6–8 hrs)

**Actions:**
- 2–3 min Loom walkthrough: problem → architecture → demo → results
- Write GitHub README:
  - Problem statement (for the client, not you)
  - Architecture diagram (Excalidraw → PNG)
  - Setup instructions
  - Demo video embed
  - Tech stack (highlight "runs on Ollama locally OR any OpenAI-compatible API")
- Export workflow JSON, anonymize credentials, commit
- Draft LinkedIn post (save, don't publish yet)

**Deliverable:** Public GitHub repo + Loom + LinkedIn draft.

**Week 1 Milestone:** You can build any AI-augmented n8n workflow end-to-end, understand production deployment, document it professionally. One flagship complete.

---

## Week 2 — Two more flagships + Go-to-market

### Day 8 — Flagship #2 design + RAG with free embeddings (~8 hrs)

**Project:** AI Customer Support Triage Agent with RAG.

**Objectives:** Embeddings, chunking, pgvector on local Postgres (from Day 5 compose), n8n Vector Store tool.

**Actions:**
- Dataset: scrape public docs (n8n docs, Stripe docs) OR use a free public support ticket dataset
- Embed with Ollama `nomic-embed-text` OR Gemini embeddings (free)
- Store vectors in local pgvector
- Build ingestion workflow: documents → chunker → embeddings → vector store

**Deliverable:** Local vector DB with 100+ chunks, retrieval tested on 5 sample queries.

---

### Day 9 — Flagship #2 build: Support Triage Agent (~8–10 hrs)

**Architecture:** Manual trigger / email sim → extract ticket → vector retrieval (top 5 chunks from pgvector) → Ollama/Gemini drafts reply grounded in context → auto-tag → Airtable → Telegram human-review queue.

**Actions:**
- Build end-to-end with fake inbound emails
- Hallucination guardrail: low retrieval confidence → flag "needs human"
- Escalation rules: "refund" or "legal" keywords → bypass AI
- Test with 10–15 diverse tickets

**Deliverable:** Triage agent, <5% hallucination rate.

**Checkpoint:** Human-in-the-loop is the selling point. Record a demo specifically showing the escalation behavior.

---

### Day 10 — Flagship #3 build: Document Processing (~8–10 hrs)

**Project:** PDF/image in → structured data out.

**Architecture:** File upload (folder watch or ngrok webhook) → OCR via Tesseract (local, free) or Gemini Vision (free tier) → LLM extraction with JSON schema (Ollama/Gemini) → Google Sheets/Airtable → notification.

**Actions:**
- Pick invoice parsing (easiest to demo), resume parsing, or contract clause extraction
- Build full pipeline with structured JSON schema
- Handle: low-quality scans, foreign language, missing fields
- Test with 10 diverse documents

**Deliverable:** Pipeline with >85% extraction accuracy.

---

### Day 11 — Polish all three + monitoring (~6–8 hrs)

**Quality bar day.**

For each workflow:
- Monitoring: error webhook → Telegram alert
- Execution logging (n8n built-in + custom logging to Postgres)
- Cost tracking: log token counts (even for Ollama — shows you think about cost)
- Polish READMEs to client-deliverable quality
- Re-record weak Loom videos

**Deliverable:** 3 workflows, 3 GitHub repos, 3 Loom videos — all production-grade.

---

### Day 12 — Portfolio site + positioning (~6–8 hrs)

**Actions:**
- Build one-pager on Carrd free tier OR GitHub Pages OR Cloudflare Pages (all free)
  - Hero: "I build AI automation systems that replace 20+ hours of manual work per week"
  - 3 case studies with Loom embeds + architecture diagrams
  - Services with 3 fixed-price packages
  - Contact form → pipe to your n8n workflow (dogfooding)
- Rewrite Upwork profile — headline: "AI Automation Engineer | n8n + LLM Workflows | Self-hosted & Production-ready"
- Update LinkedIn headline + About

**Deliverable:** Live portfolio URL, Upwork profile, LinkedIn updated.

---

### Day 13 — Service packages + outreach assets (~6–8 hrs)

**Packages:**

| Package | Price | Scope |
|---------|-------|-------|
| AI Lead Qualification Pipeline | $1,500 | Setup + 1 CRM integration + 1-week support |
| AI Customer Support Triage | $3,500 | RAG setup + 1 helpdesk integration + training |
| Document Processing Automation | $2,500 | One document type + structured output + 2-week support |
| Monthly retainer | $2,000/mo | Maintenance + one new workflow per month |

**Actions:**
- 3 case study one-pagers (Canva free)
- 3 cold outreach templates (LinkedIn DM, cold email, Upwork proposal)
- Target list: 50 prospects (SMB SaaS, agencies, D2C e-commerce)
- 10 Upwork proposal templates

---

### Day 14 — Launch day (~8 hrs) — first optional spend

**Decision:** Spin up $6 VPS now, or stay on ngrok for another week?

- **Zero cash:** stay on ngrok, warn prospects URLs are temporary. Fine for initial calls.
- **Can spare $6:** Hetzner CPX11, deploy your Day 5 compose, point `n8n.yourdomain.com` at it. Live demo URLs convert better.

**Launch actions (in order):**
1. Post LinkedIn: breakdown of your most visual workflow
2. Submit 10 targeted Upwork proposals
3. Send 30 LinkedIn DMs to prospect list
4. Apply to Contra, Toptal, Jobbers.io
5. Post in r/n8n and r/automation (value-first content linking to portfolio)
6. Answer 3 substantive questions in n8n community forum

**Deliverable:** You are in the market.

---

## Post-sprint: Weeks 3–4 reality check

- Zero signed contracts in the first 7 days = normal
- First contract by Week 3–4 if you're consistent (20 outreach touches/day)
- First rate: $40–60/hr on Upwork for first 2–3 gigs, then climb
- First package sale: $1,500–$3,500 within 30 days

---

## When to start paying

| Trigger | Spend | Why |
|---------|-------|-----|
| First client call booked | $6 VPS | Credibility — live demo URL |
| First contract signed | Infra the project needs | Pass costs to client |
| Monthly revenue > $2K | Paid API credits, monitoring | Reliability for paying clients |
| Monthly revenue > $5K | Portfolio Pro tools | Marginal improvements on top of revenue |

**Rule:** Every dollar out should be triggered by a dollar in or a confirmed prospect on the hook.

---

## Common pitfalls

1. Tutorial hell — after Day 3, build, don't watch
2. Perfectionism — 80% shipped > 100% hidden
3. Skipping DevOps (Day 5) — it's the moat
4. Waiting for inbound — cold outreach is 70% of early revenue
5. Underpricing — don't bid $20/hr
6. Not logging LLM costs — even on Ollama, log tokens
7. Treating Ollama as "just as good" — it isn't for complex reasoning; know when to switch

---

## Weekly checkpoints

**End of Day 7:**
- [ ] Local n8n + Ollama + free cloud LLMs integrated
- [ ] Flagship #1 complete with Loom + README + GitHub
- [ ] Comfortable wiring any REST API
- [ ] Docker Compose + deployment README polished

**End of Day 14:**
- [ ] 3 flagships complete
- [ ] Portfolio site live
- [ ] Upwork + LinkedIn + Contra profiles updated
- [ ] 10 Upwork proposals submitted
- [ ] 30 LinkedIn DMs sent
- [ ] First LinkedIn case study posted

---

## Resources (all free)

**Official:**
- n8n docs: `docs.n8n.io`
- n8n Academy: `n8n.io/courses`
- n8n templates: `n8n.io/workflows`
- Ollama docs: `ollama.com/docs`

**YouTube:** n8n official, Leon van Zyl, Nick Saraev (sales angle)

**LLM + RAG:** Anthropic prompt engineering guide, Pinecone learning center, Ollama library (`ollama.com/library`)

**Community:** `community.n8n.io`, r/n8n, r/LocalLLaMA

---

**Final note:** Zero cost means zero excuses. Block the hours before Day 1.
