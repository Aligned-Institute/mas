# Signals v3 Design Brief

## Purpose

Signals v3 is a multi-agent data alignment layer for enterprise decision making.

Every large organization faces the same structural problem: internal operating data lives in one world, external market signals live in another, and the work of reconciling them into a coherent picture happens manually — in spreadsheets, Monday morning emails, analyst decks, and meetings. That reconciliation work is slow, fragile, and impossible to audit. It consumes the time of the people who should be making decisions, not preparing to make them.

Signals v3 automates that reconciliation through a coordinated architecture of specialized agents that fetch, align, and resolve data from disparate sources before any synthesis or decision artifact is produced. The output is a continuously maintained, versioned, conflict-resolved data state that every downstream tool — dashboards, briefs, workflows, watchlists, governance controls — is built on top of.

This is what makes Signals v3 different from every intelligence tool built on top of a single LLM call. Those tools retrieve and concatenate. Signals v3 aligns.

## Product Thesis

Large companies already have data, dashboards, reports, analysts, spreadsheets, and market feeds. The hard problem is that those inputs are fragmented. Internal operating data lives in one world, external market data lives in another, and human decision workflows often happen in meetings, emails, decks, and manually maintained trackers.

Signals v3 is designed to bridge those worlds — and specifically to automate the alignment of data across those worlds before any synthesis or decision artifact is produced.

The core commercial problem is not data access. It is data alignment. Analysts spend hours every week not because data is hard to find, but because reconciling signals from incompatible sources into a coherent, trustworthy picture is manual, fragile, and non-reproducible. Signals v3 automates that reconciliation layer through a multi-agent data alignment architecture. The aligned state it produces is the product that downstream decision-making tools are built on.

It should help a user answer questions like:

- What external signals matter to our business right now?
- How do those signals connect to our suppliers, contracts, inventory, customers, regions, and strategy?
- What evidence supports the answer?
- What should we monitor next?
- What dashboard, brief, workflow, or artifact should this become?

## Core Product Model

```text
[ CORE — always delivered ]
Internal LLM Runtime
    -> CONX Base Connectors -> Signal Engine (parallel agent fetch)
    -> Alignment Coordinator (conflict detection, freshness reconciliation, confidence weighting)
    -> Canonical Aligned State  ← standard interface for all modules
    -> Sage / iSLM / Signals v3 Brain (base reasoning)
    -> Output Factory (briefs, dashboards)

[ MODULES — customer-activated ]
  Module A: Extended Connectors   (ERP, procurement, custom APIs)
  Module B: Governor              (access control, lineage, compliance)
  Module C: Vector Memory         (conversation history, semantic recall)
  Module D: Graph Memory          (entity relationships, supplier maps)
  Module E: Decision Memory       (outcome tracking, decision audit)
  Module F: Extended Output       (workflows, watchlists, recurring surfaces)
```

The Canonical Aligned State is the standard interface between the core and every module. Modules do not depend on each other — a client can activate the Governor without Memory, or Vector Memory without Graph Memory. Each module plugs into the aligned state and extends what can be done with it, without requiring changes to the components below it.

This is the commercial architecture: land with the core alignment layer and prove its value in weeks. Expand with capability modules as the client's needs grow, their data maturity increases, or their compliance requirements demand it.

### Data Fabric

The data fabric is the source universe. It includes external sources such as markets, macro data, news, regulation, weather, competitors, supplier events, and public filings. It also includes company context such as ERP, procurement, contracts, inventory, uploads, analyst files, and private operating knowledge.

### CONX / Connectors

CONX is the connector layer between source systems and the signal engine. It represents the practical access paths into data: APIs, files, databases, tools, uploads, and future source-specific adapters. CONX is intentionally thin. It connects; it does not reason.

### Signal Engine

The Signal Engine coordinates multiple specialized agents running in parallel — each agent owns a single data domain (commodity prices, macro indicators, news feeds, internal ERP, procurement data, domain-specific formulas). Parallel fetch is the default; sequential is a fallback for dependency-ordered sources.

Each agent returns data with attached metadata: what it found, when the data was last updated, the source it came from, and a per-source confidence score. The Signal Engine does not synthesize or reason across agents. It fetches, labels, and hands off.

### Alignment Coordinator

The Alignment Coordinator sits between the Signal Engine and the Signals v3 Brain. It is the component responsible for turning parallel agent outputs into a coherent, usable data state. This is the most important component Signals v3 introduces and the one most absent from existing market alternatives.

It handles four responsibilities:

**Conflict detection.** When agents return contradictory signals — for example, the commodity agent shows stable prices while the news agent flags a supply disruption that has not yet moved spot prices — the coordinator does not silently concatenate both. It surfaces the inconsistency, labels it as a leading indicator gap, and preserves both signals with their disagreement annotated.

**Freshness reconciliation.** Agents report the age of their data. The coordinator compares freshness across agents and flags combinations where stale data sits alongside live data in ways that could mislead synthesis. A macro data point from yesterday alongside a spot price from two minutes ago is not automatically trustworthy context — it depends on what question is being asked.

**Confidence weighting.** Each agent contributes a per-source confidence score. The coordinator aggregates these into a unified confidence rating for the aligned state and surfaces that rating to the Governor and to the user.

**Canonical aligned state.** The output of the Alignment Coordinator is a versioned, structured data object: attributed (every data point is traceable to its source and agent), conflict-annotated (disagreements are labeled, not resolved silently), freshness-stamped, and confidence-weighted. This object — not a raw context string — is what the Signals v3 Brain consumes.

The aligned state is versioned so that Monday's state can be diffed against last Monday's. It is structured so that downstream tools (dashboards, briefs, workflows, watchlists) can query it directly rather than re-running synthesis. It is the foundation on which everything else in Signals v3 is built.

### Signals v3 Brain

The Signals v3 Brain is the intelligence core. It contains vector memory, graph memory, decision memory, business context, synthesis, and reasoning. This is where signal context, company knowledge, and prior decisions become usable intelligence.

### Sage and iSLM

Sage is the user-facing conversation and workbench layer. Users talk to Sage.

iSLM is the lightweight routing layer under Sage. It decides how the ask should enter the brain: graph memory, vector memory, live signal context, internal company context, direct answer, or hybrid route.

```text
User -> Sage -> iSLM Router -> Signals v3 Brain -> governed output
```

### Governor

Governor is the control boundary around the Sage, iSLM, and Signals v3 Brain interaction. It controls access, lineage, source freshness, confidence thresholds, escalation, refusal, output publishing, and save-to-memory behavior.

Governor answers questions like:

- Is this user allowed to ask this?
- Which memory and source systems can be used?
- Is the evidence fresh enough?
- Does confidence require caveats or escalation?
- Should the system answer, ask a clarifying question, qualify, refuse, or save the decision?

### Output Factory

The output factory turns intelligence into usable business artifacts: dashboards, executive briefs, workflow queues, watchlists, source packs, visualizations, HTML documents, and reusable decision experiences.

This is where Signals v3 becomes more than chat. A useful answer can become a dashboard, a recurring brief, a workflow, a watchlist, a board-ready explanation, or a reusable operating surface.

## Capability Tiers

Signals v3 is delivered in a core layer plus optional modules. Every module plugs into the Canonical Aligned State. No module requires another module to be active. Clients choose what to activate based on their readiness, compliance requirements, and use case maturity.

### Core (always included)

The core is the alignment layer. It is what Signals v3 is. Without it, everything else is just another dashboard or chat interface.

| Component | What it does |
|---|---|
| Internal LLM Runtime | Runs all orchestration and synthesis on customer infrastructure. Mandatory — see Data Sovereignty. |
| CONX Base Connectors | Market data, macro indicators, news feeds, file upload. The minimum viable data fabric. |
| Signal Engine | Dispatches specialized agents in parallel. Each agent owns one data domain. Returns labeled data with source metadata. |
| Alignment Coordinator | Detects conflicts across agents, reconciles freshness mismatches, weights confidence, and produces the Canonical Aligned State. |
| Canonical Aligned State | Versioned, attributed, conflict-annotated data object. The standard interface everything else is built on. |
| Sage + iSLM (base) | User conversation layer with intelligent routing across the aligned state. |
| Output Factory (base) | Briefs and dashboards generated from the aligned state. |

### Module A — Extended Connectors

Adds company-specific and proprietary data sources beyond the base connectors: ERP systems, procurement databases, contract repositories, inventory feeds, and custom API adapters. Enables CONX to map external signals directly to internal entities — suppliers, products, regions, contracts.

Activate when the client is ready to connect internal operating data and move beyond market-signal-only analysis.

### Module B — Governor

Adds the control boundary: user access control, source permission rules, freshness thresholds, confidence gates, escalation paths, refusal rules, output publishing controls, and lineage tracking. Governor answers whether an answer is allowed, qualified, escalated, refused, or saved — and preserves an audit trail of each decision.

Activate when the client operates in a regulated industry, has compliance requirements, or needs formal access control and decision audit trails.

### Module C — Vector Memory

Adds semantic memory over conversation history and past answers. Enables contextual follow-up questions ("what changed since last week?"), recall of prior analysis, and semantic search over the accumulated intelligence the platform has produced.

Activate when the client wants persistent context across sessions and the ability to surface relevant prior work.

### Module D — Graph Memory

Adds relational entity knowledge: supplier-to-product mappings, product-to-region exposure, counterparty relationships, value chain connections. Enables the intelligence layer to answer questions like "which of our suppliers are exposed to this market move?" without requiring the user to provide that context in every query.

Activate when the client has complex multi-entity relationships that external signals need to be mapped against.

### Module E — Decision Memory

Adds tracking of what was decided, when, on what evidence, with what assumptions, and what the outcome was. Enables retrospective analysis ("was that call right?"), pattern recognition over past decisions, and accountability for decision quality over time.

Activate when the client wants to close the loop between intelligence and outcomes.

### Module F — Extended Output Factory

Adds workflow queues, recurring watchlists, scheduled alert surfaces, and reusable decision experiences beyond the base brief and dashboard. Turns a useful answer into a recurring operating surface that a team uses without re-running synthesis.

Activate when the client wants to embed intelligence into repeating business processes rather than treating each query as one-off.

### Module Proof Targets

Each module has its own proof target. A module is considered proven when it can demonstrate its capability in isolation against a realistic business scenario — independently of whether other modules are active.

---

## Engineering Specifications — MVP

### Technology Stack

| Layer | Technology | Notes |
|---|---|---|
| **Orchestration LLM** | `gemma3:4b` via Ollama | Routing, conflict detection, agent coordination. Temp 0.0. High-frequency. |
| **Synthesis LLM** | `deepseek-r1:14b` via Ollama | Final synthesis, complex reasoning. Called once per query post-alignment. |
| **Embedding model** | `nomic-embed-text` via Ollama | Vector memory, RAG retrieval, semantic search. |
| **External LLM (opt-in)** | Claude claude-sonnet-4-6 (Anthropic) | Synthesis escalation only. Sanitized context. Never used for orchestration. |
| **Vector database** | Supabase pgvector | Aligned state storage, vector memory, source registry. Postgres-native. |
| **Graph database** | Neo4j Community (Module D) | Entity relationships: supplier → product → region. Neo4j Aura Free for dev. |
| **Cache / queue** | Redis (self-hosted on Droplet) | Agent output caching, freshness TTLs, Celery task broker. |
| **Backend runtime** | Python 3.11 + FastAPI | Async throughout. Matches v2 stack. |
| **Agent framework** | `asyncio` + `LangGraph` | asyncio for parallel Signal Engine fetch; LangGraph for Alignment Coordinator stateful flow. |
| **Task scheduler** | Celery + Redis | Scheduled agent refreshes, async brief generation, watchlist triggers. |
| **Auth** | Supabase Auth | JWT-based, RLS on all tenant data. Row-level isolation per tenant. |
| **Frontend** | Next.js 15 + React 19 + TypeScript | App Router. Matches v2 stack with version bump. |
| **UI components** | shadcn/ui + Radix UI + TailwindCSS | Same as v2. |
| **Data visualization** | Recharts + D3 | Recharts for standard charts; D3 for custom aligned state visualizations. |
| **Real-time** | Supabase Realtime | Pushes aligned state updates to dashboard without polling. |
| **Export** | `@react-pdf/renderer` + `xlsx` | PDF briefs, Excel exports. Same as v2. |
| **Containers** | Docker + Docker Compose | Local dev and staging. Single `docker-compose.yml` for full stack. |
| **API server hosting** | DigitalOcean Droplet | 8 vCPU / 16 GB RAM minimum for API + frontend + Redis. |
| **LLM/GPU hosting** | Paperspace Core (A100 40GB) or DigitalOcean GPU Droplet | Runs Ollama with orchestration + synthesis models concurrently. |
| **Managed DB** | Supabase (cloud) | Postgres + pgvector + Auth + Realtime. Matches v2. |

---

### Internal LLM Runtime

**Orchestration model — `gemma3:4b` (Ollama)**
- Purpose: iSLM routing, Alignment Coordinator conflict detection, freshness assessment, per-agent domain classification
- Temperature: 0.0 (deterministic; all orchestration decisions must be auditable and reproducible)
- Call volume: 4–12 calls per user query depending on active modules and route
- Context window: 128k tokens (sufficient for multi-agent output comparison in Alignment Coordinator)
- Fallback: `gemma2:2b` (lower quality but available on smaller GPU)

**Synthesis model — `deepseek-r1:14b` (Ollama)**
- Purpose: Final answer generation from the Canonical Aligned State, artifact generation (briefs, summaries, HTML)
- Temperature: 0.3–0.7 (configurable per artifact type)
- Call volume: 1 call per user query, after alignment is complete
- Thinking tags: Extract `<think>...</think>` content and surface as optional "reasoning trace" in UI
- Fallback: `deepseek-r1:8b` (lower parameter count; acceptable for most synthesis tasks)
- Upgrade path: `deepseek-r1:70b` (Q4 quantized) for clients with A100 80GB

**Embedding model — `nomic-embed-text` (Ollama)**
- Purpose: Vector memory indexing, RAG retrieval over company documents, semantic search
- Dimensionality: 768
- Used by: Supabase pgvector (cosine similarity, threshold 0.3, top-k 3 default)
- Fallback: `mxbai-embed-large` (higher quality, same Ollama deployment)

**Minimum GPU hardware:**
- Development: NVIDIA RTX 4090 (24 GB VRAM) — runs `gemma3:4b` + `deepseek-r1:8b` concurrently
- Production MVP: NVIDIA A10G (24 GB VRAM) on Paperspace — same as development, with Ollama daemon
- Recommended production: NVIDIA A100 40 GB — runs `gemma3:4b` + `deepseek-r1:14b` concurrently with headroom

**Ollama deployment:**
- Runs as a persistent daemon on the GPU host
- Exposes `http://gpu-host:11434` to the FastAPI backend
- Models pre-loaded on startup: `gemma3:4b`, `deepseek-r1:14b`, `nomic-embed-text`
- CPU fallback: `gemma3:4b` + `deepseek-r1:8b` run on CPU for demo/dev environments without GPU (degraded latency)

---

### Infrastructure and Hosting

```text
[ Client Browser ]
       ↓ HTTPS
[ DigitalOcean Droplet — 8 vCPU / 16 GB ]
  ├── Next.js 15 (port 3000, served via PM2)
  ├── FastAPI (port 8000, served via uvicorn + PM2)
  └── Redis (port 6379, local)
       ↓ internal network
[ Paperspace A100 GPU Host ]
  └── Ollama daemon (port 11434)
       └── gemma3:4b  (orchestration)
       └── deepseek-r1:14b  (synthesis)
       └── nomic-embed-text (embeddings)
       ↓ managed cloud
[ Supabase ]
  ├── Postgres + pgvector  (aligned states, vector memory, source registry, agent catalog)
  ├── Supabase Auth        (JWT, RLS, tenant isolation)
  └── Supabase Realtime    (aligned state push to frontend)
       ↓ Module D only
[ Neo4j Aura ]
  └── Graph memory: entity relationships
```

**Deploy scripts:** `/opt/csee/deploy.sh` (API + frontend), matching v2 PM2 pattern.
- Stop PM2 → `npm run build` → restart PM2 (never build while PM2 is running)
- Separate scripts for GPU host: pull new Ollama models, restart daemon

---

### Database Architecture

**Supabase Postgres — core tables**

```sql
-- Source registry: all CONX connectors registered here
CREATE TABLE source_registry (
  id            uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id     uuid NOT NULL,
  source_name   text NOT NULL,          -- e.g. "yfinance_commodity"
  domain        text NOT NULL,          -- commodity | macro | news | internal | upload
  connector     text NOT NULL,          -- python class name
  freshness_ttl interval NOT NULL,      -- e.g. '2 minutes', '1 day'
  confidence    float NOT NULL,         -- 0.0–1.0 default confidence for this source
  active        boolean DEFAULT true,
  config        jsonb                   -- connector-specific config (API keys refs, endpoints)
);

-- Canonical Aligned State: one record per alignment run
CREATE TABLE aligned_states (
  id                  uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id           uuid NOT NULL,
  version             integer NOT NULL,
  query_context       text,
  agents              jsonb NOT NULL,   -- array of AgentOutput objects
  conflicts           jsonb,            -- array of ConflictRecord objects
  aggregate_confidence float NOT NULL,
  freshness_flags     jsonb,
  aligned_at          timestamptz DEFAULT now(),
  state_hash          text NOT NULL     -- deterministic hash for deduplication
);

-- Vector memory: indexed embeddings for semantic search (Module C)
CREATE TABLE vector_memory (
  id          uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id   uuid NOT NULL,
  content     text NOT NULL,
  embedding   vector(768),              -- nomic-embed-text dimensionality
  source_type text,                     -- aligned_state | conversation | brief | document
  source_id   uuid,
  created_at  timestamptz DEFAULT now()
);
CREATE INDEX ON vector_memory USING ivfflat (embedding vector_cosine_ops);

-- Decision memory: tracked decisions and outcomes (Module E)
CREATE TABLE decision_memory (
  id              uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id       uuid NOT NULL,
  decided_at      timestamptz NOT NULL,
  question        text NOT NULL,
  answer_summary  text NOT NULL,
  aligned_state_id uuid REFERENCES aligned_states(id),
  assumptions     jsonb,
  outcome         text,                 -- filled in retrospectively
  outcome_at      timestamptz
);

-- Brief and artifact archive
CREATE TABLE artifacts (
  id          uuid PRIMARY KEY DEFAULT gen_random_uuid(),
  tenant_id   uuid NOT NULL,
  type        text NOT NULL,            -- brief | dashboard | watchlist | workflow
  title       text,
  content     text,
  aligned_state_id uuid REFERENCES aligned_states(id),
  created_at  timestamptz DEFAULT now()
);
```

**RLS:** All tables enforce `tenant_id = auth.jwt() ->> 'tenant_id'` so tenant data is fully isolated.

**Redis — cache keys**
- `agent:{tenant}:{domain}:output` → cached AgentOutput JSON, TTL = source freshness_ttl
- `aligned_state:{tenant}:{hash}` → cached CanonicalAlignedState, TTL = 5 minutes
- `celery:*` → Celery task queue and result backend

**Neo4j (Module D only) — graph schema**
```
(:Company {id, name, tenant_id})
(:Supplier {id, name, country})
(:Product {id, name, category})
(:Region {id, name})
(:MarketSignal {id, type, value, timestamp})

(:Company)-[:SOURCES_FROM]->(:Supplier)
(:Supplier)-[:PRODUCES]->(:Product)
(:Product)-[:SOLD_IN]->(:Region)
(:MarketSignal)-[:AFFECTS]->(:Product)
(:MarketSignal)-[:AFFECTS]->(:Supplier)
```

---

### Signal Engine — Agent Catalog

Each agent is a Python class implementing the `BaseAgent` interface: `fetch() -> AgentOutput`.

**Core agents (always deployed):**

| Agent | Data source | Library | Key data points | Freshness TTL |
|---|---|---|---|---|
| `CommodityAgent` | Yahoo Finance | `yfinance` | WTI, Brent, NatGas, XLB, S&P 500, EUR/USD, USD/CNY | 2 minutes |
| `MacroAgent` | FRED API | `fredapi` | Fed funds rate, CPI, GDP, housing starts, industrial production, PPI | 1 day |
| `NewsAgent` | RSS feeds | `feedparser` | ICIS, Chemical Week, OilPrice.com, Reuters commodities | 15 minutes |
| `DomainAgent` | Internal formulas | — | Feedstock chain margins (crude → naphtha → benzene → aniline → MDI). Configurable per vertical. | Derived on fetch |
| `UploadAgent` | Client file upload | `pandas`, `openpyxl` | Excel/CSV analysis against current aligned state | On upload |

**Module A agents (Extended Connectors):**

| Agent | Data source | Integration method | Freshness TTL |
|---|---|---|---|
| `ERPAgent` | SAP / Oracle / NetSuite | REST API adapter (client-specific config in `source_registry.config`) | 1 hour |
| `ProcurementAgent` | Procurement DB | SQL connector or REST API | 4 hours |
| `ContractAgent` | Contract repository | Document parse + vector index | On ingest |
| `CustomAPIAgent` | Client-defined | Generic REST adapter; URL + auth config in source registry | Configurable |

**AgentOutput schema (all agents return this):**
```json
{
  "agent_id": "commodity_agent",
  "domain": "commodity",
  "source": "yfinance",
  "fetched_at": "2026-06-26T14:32:00Z",
  "freshness_status": "fresh",
  "confidence": 0.95,
  "data": { ... },
  "raw_text": "WTI: $83.42 (+1.2%) ..."
}
```

Agents run concurrently via `asyncio.gather()`. Each agent writes to a shared results list. The Signal Engine collects all `AgentOutput` objects and passes the array to the Alignment Coordinator.

---

### Alignment Coordinator — Technical Specification

**Input:** `List[AgentOutput]` from the Signal Engine.

**Step 1 — Freshness check (rule-based, no LLM)**
Compare each agent's `fetched_at` against its `freshness_ttl` from `source_registry`. Flag any agent whose data exceeds TTL as `stale`. Flag combinations where stale and fresh agents are mixed on the same question as `freshness_mismatch`.

**Step 2 — Conflict detection (LLM call: `gemma3:4b`, temp 0.0)**
Prompt the orchestration model with a structured comparison of all agent raw_text outputs. System prompt instructs it to identify contradictions, leading indicator gaps, and directional mismatches. Returns a structured `ConflictRecord` array.

```json
{
  "agents": ["commodity_agent", "news_agent"],
  "type": "leading_indicator",
  "description": "CommodityAgent reports WTI stable at $83. NewsAgent reports OPEC supply cut announcement. Price has not yet moved — news leads price.",
  "resolution": "annotated"
}
```

**Step 3 — Confidence aggregation (rule-based)**
Weighted average of per-agent confidence scores. Conflicts reduce the aggregate confidence by a configurable penalty (default: -0.1 per conflict). Freshness mismatches reduce by -0.05 per flagged agent.

**Step 4 — Produce Canonical Aligned State**
Assemble all agents, conflicts, freshness flags, and aggregate confidence into a single `CanonicalAlignedState` JSON object. Write to `aligned_states` table in Supabase. Push update via Supabase Realtime to connected frontends.

**Total Alignment Coordinator latency target:** < 3 seconds (excluding agent fetch time, which runs in parallel).

---

### iSLM Router — Technical Specification

- **Model:** `gemma3:4b` (Ollama, temp 0.0)
- **Input:** User query string + active module list + available agent list
- **Output:**
  ```json
  {
    "route": "hybrid",
    "agents": ["commodity_agent", "macro_agent", "news_agent"],
    "rag_query": "feedstock margin compression benzene",
    "memory_types": ["vector"],
    "confidence": 0.87,
    "reasoning": "Question references current market conditions (MCP) and prior analysis (vector memory)."
  }
  ```
- **Routes:** `mcp` (live agents only) | `rag` (document retrieval only) | `hybrid` (both) | `direct` (no external data) | `vector` (Module C, conversation memory) | `graph` (Module D, entity relationships) | `internal` (Module A, ERP/procurement)
- **Confidence threshold:** 0.6 — below this, force `hybrid` route regardless of predicted route
- **Escalation:** If `confidence < 0.4`, Sage prompts user for clarification before routing

---

### Backend API Endpoints

```
POST /align          — Run Signal Engine + Alignment Coordinator. Returns CanonicalAlignedState.
POST /query          — Full pipeline: align → iSLM route → Brain synthesis → governed output.
POST /route          — iSLM classification only. Returns routing decision without execution.
GET  /state/latest   — Retrieve latest CanonicalAlignedState for tenant.
GET  /state/{id}     — Retrieve specific aligned state by ID (for diff/audit).
POST /upload         — Ingest file via UploadAgent. Returns agent output + triggers realignment.
POST /artifact       — Generate artifact (brief, dashboard, watchlist) from aligned state.
GET  /artifacts      — List all artifacts for tenant.
GET  /health         — Service health + model availability check.

# Module B
GET  /governor/log   — Lineage and decision log for tenant.

# Module C
GET  /memory/search  — Semantic search over vector memory. Query param: ?q=<query>

# Module D
GET  /graph/query    — Cypher query interface to Neo4j graph memory.

# Module E
GET  /decisions      — Decision memory log for tenant.
POST /decisions/{id}/outcome — Record outcome against a past decision.
```

---

### Frontend Pages

| Route | Name | Description |
|---|---|---|
| `/dashboard` | Signal Dashboard | Live commodity cards, macro indicators, news ticker, aligned state confidence badge |
| `/alignment` | Alignment Inspector | Visual diff of current vs. prior aligned state; conflict annotations; freshness map |
| `/sage` | Sage Workbench | Chat interface + route trace + source attribution + aligned state reference |
| `/leaderboard` | Signal Board | Sortable anomaly table from aligned state |
| `/portfolio` | Artifact Archive | Brief viewer, dashboard library, watchlist manager |
| `/upload` | Data Analyzer | File upload → UploadAgent → realignment trigger |
| `/ecosystem` | Value Chain | Domain-specific relationship map (configurable per vertical) |
| `/foundation` | Intel Reports | Long-form research and indexed company documents |
| `/settings` | Tenant Config | Source registry management, module toggles, freshness threshold config |

---

## How People Use It

### Analyst

An analyst asks Sage what changed, inspects the source trail, checks confidence and freshness, and turns the result into a brief or dashboard view.

### Executive

An executive asks for the current business implication, receives a concise answer with evidence and caveats, and can request a board-ready artifact.

### Procurement or Operations

A team member asks how a market signal affects suppliers, contracts, inventory, regions, or exposure. Signals v3 connects live signal context to internal company context and produces next actions.

### Strategy or Finance

A strategy or finance user asks for scenario implications, supporting evidence, assumptions, and recurring monitoring surfaces.

## What Makes Signals v3 Different

Signals v3 is designed around six product principles:

1. Align data across agents before synthesizing it.
2. Combine internal and external context.
3. Make the route and evidence visible.
4. Treat freshness, confidence, and lineage as first-class product features.
5. Turn conversations into durable artifacts.
6. Govern what can be used, answered, published, or saved.

Principle one is the technical foundation the others depend on. Without a dedicated alignment layer, principles two through six operate on concatenated strings rather than on a coherent, conflict-resolved data state. The alignment step is what separates Signals v3 from a wrapper around an LLM with tool calls.

## Product Proof Targets

The application should prove itself through visible product capabilities:

### Multi-Agent Data Alignment

Proof: given a business question that requires three or more data domains, Signals v3 dispatches specialized agents in parallel, detects at least one conflict or freshness mismatch between them, produces a canonical aligned state with conflict annotations and confidence scores, and makes the aligned state inspectable before synthesis begins. The downstream answer is demonstrably traceable to the aligned state, not to raw agent output.

### Signal Engine

Proof: Signals v3 can ingest source context, normalize it, label freshness and confidence, connect it to company context, and show how that signal enters the brain.

### Sage / iSLM / Brain

Proof: a user can ask a business question, Sage captures intent, iSLM chooses a route, and the Signals v3 Brain returns an answer with route, sources, assumptions, confidence, and memory behavior.

### Output Factory

Proof: Signals v3 can turn intelligence into useful artifacts: dashboard views, briefs, watchlists, source packs, workflows, or HTML experiences.

### Memory

Proof: Signals v3 can store and inspect graph memory, vector memory, decision memory, source lineage, and company context without polluting future answers.

### Governor

Proof: Signals v3 can show why an answer is allowed, qualified, escalated, refused, or saved.

### Evaluation

Proof: Signals v3 can run representative questions through expected routes, source requirements, freshness checks, memory behavior, and output usefulness criteria.

## Build Platform Note

Signals v3 is built using the Strattegys build platform, including the eight-factor autonomous layer and the Strattegys autonomous build queue controlled through DevMaster. That platform enables Signals v3 to be continuously improved through human-enabled autonomous coding work.

For Anthony, the important point is simple: the application is designed to evolve continuously. New product capabilities, data connectors, intelligence behaviors, output types, and governance rules can be proposed, built, proved, reviewed, and improved over time.

## Data Sovereignty and Internal LLM Architecture

Signals v3 mandates internal LLM deployment. This is not a preference inherited from the v2 chemicals fork. It is a structural requirement of the multi-agent architecture.

### Why External LLMs Cannot Serve as the Orchestration Layer

Multi-agent coordination is LLM-intensive by design. A single user query in Signals v3 triggers an LLM call at each of the following points: iSLM routing, per-agent domain reasoning where needed, conflict detection in the Alignment Coordinator, confidence assessment, synthesis, and output formatting. That is six to fifteen LLM calls per query depending on the active modules and route taken.

If each of those calls goes to an external API, four problems compound:

**Cost.** At scale, per-call API pricing applied to orchestration-volume LLM calls becomes commercially unviable. The orchestration layer is not a one-call-per-query interface — it is a continuous reasoning process.

**Latency.** Each external API call adds a network round-trip. Chained orchestration over external APIs produces response times that degrade the product experience regardless of model quality.

**Data egress.** The Alignment Coordinator reasons over company data — procurement records, contract terms, ERP state, internal operating knowledge. That data cannot be sent to a third-party API without violating data sovereignty requirements. For finance, energy, defense, healthcare, and regulated manufacturing clients, data egress is not a configurable risk — it is a disqualifying condition.

**Dependency.** External API availability, rate limits, pricing changes, and model deprecations are outside the client's control. An enterprise intelligence product built on a dependency the client does not own carries operational risk that enterprise procurement will identify and reject.

### The Internal LLM Mandate

Signals v3 runs on customer infrastructure. The LLM runtime is internal. No proprietary data leaves the client's environment as part of the orchestration or alignment process.

The runtime model is two-tier:

**Orchestration model (small, fast, high-frequency).** A lightweight model — comparable to gemma2:2b or similar — handles routing, conflict detection, agent coordination, and freshness assessment. It runs locally via Ollama or equivalent. Temperature is set at or near zero for deterministic, auditable decisions. This model is called many times per query.

**Synthesis model (larger, lower-frequency).** A capable reasoning model — comparable to DeepSeek-R1 8B or larger — handles final synthesis, complex cross-domain reasoning, and artifact generation. It also runs locally. This model is called once per query after the aligned state is prepared.

**External LLM escalation (opt-in only).** A client may choose to route final synthesis to an external model for tasks where model quality outweighs data sensitivity. This is a client-controlled opt-in, applies only to synthesis, and requires explicit confirmation that no proprietary data is included in the synthesis context. It is not the default and it is never used for orchestration or alignment.

### Data Sovereignty as a Commercial Differentiator

For enterprise clients in regulated industries, the internal LLM mandate is not a technical constraint — it is a selling point. Signals v3 runs on your infrastructure, uses your compute, and keeps your data inside your boundary. The intelligence it produces belongs to you. The aligned state it maintains is stored in your environment. No vendor has access to your signals, your decisions, or your operating context.

This is what makes it possible to connect Module A (Extended Connectors) to ERP and procurement systems. Clients will not connect proprietary internal systems to a product that sends that data externally. The internal architecture is the prerequisite for the most commercially valuable capability in the stack.

## Build Priority Note

Build the core before any module. A sophisticated governance or memory layer built on top of a concatenation architecture does not solve the problem. A minimal governance or memory layer built on top of a genuine alignment layer does. The right build order:

**Core (required before any module):**
1. Internal LLM runtime (Ollama + orchestration model + synthesis model)
2. Signal Engine with parallel agent fetch and per-source metadata
3. Alignment Coordinator with conflict detection, freshness reconciliation, and confidence weighting
4. Canonical Aligned State as a versioned, structured object
5. Sage + iSLM base routing over the aligned state
6. Output Factory base: briefs and dashboards from the aligned state

**Modules (customer-sequenced after core is stable):**
- Module B (Governor) is the most common first add for enterprise clients — activates before Extended Connectors if compliance comes first
- Module A (Extended Connectors) unlocks the deepest value but requires the client to have internal data integration readiness
- Module C (Vector Memory) is low-friction and high-value for teams that use the platform daily
- Modules D and E (Graph and Decision Memory) require more client-side data curation before they yield reliable results
- Module F (Extended Output) is best activated after the client has validated their core use cases and wants to embed them into recurring operations

## Migration Path — v2 → v2.5 → v3

v2 (cSignals) is production-near and architecturally sound. v3 is the target. v2.5 is the approved incremental step that introduces the alignment architecture into the existing codebase without a rewrite, no new infrastructure, and no breaking changes to v2 endpoints or deployments.

### v2.5 Scope

v2.5 is fully additive to v2. Every existing endpoint, page, and deployment pattern stays intact. The changes layer in below the synthesis call and surface through a new frontend page and a confidence indicator on the existing dashboard.

**What changes in the backend (`csignals/src/`):**

**1. Parallel agent fetch — `src/islm/executor.py`**
Replace sequential agent calls with `asyncio.gather()`. All active agents run concurrently. Each agent returns a structured `AgentOutput` object (see schema below) instead of an appended string. Estimated effort: 1 day.

**2. AgentOutput schema — `src/islm/agents/base.py` (new)**
Introduce a `BaseAgent` abstract class and `AgentOutput` dataclass. All existing agents (`CommodityAgent`, `MacroAgent`, `NewsAgent`, `DomainAgent`, `UploadAgent`) are refactored to implement `BaseAgent.fetch() -> AgentOutput`. This is the prerequisite for every other v2.5 change. Estimated effort: 2 days.

```python
@dataclass
class AgentOutput:
    agent_id: str
    domain: str          # commodity | macro | news | domain | upload
    source: str
    fetched_at: datetime
    freshness_status: str  # fresh | warning | stale
    confidence: float      # 0.0–1.0
    data: dict
    raw_text: str
```

**3. Source registry — Supabase migration (new table)**
Add `source_registry` table to Supabase (schema in Engineering Specifications). Agents read their `freshness_ttl` and default `confidence` from this table rather than hardcoded values. Enables the settings page to manage connectors without code changes. Estimated effort: 1 day.

**4. Alignment Coordinator — `src/islm/alignment.py` (new)**
New module, three steps:

- **Freshness check (rule-based):** compare each `AgentOutput.fetched_at` against `source_registry.freshness_ttl`. Flag stale agents and freshness mismatches across the agent set.
- **Conflict detection (one `gemma3:4b` call, temp 0.0):** pass all agent `raw_text` outputs to the orchestration model with a structured prompt asking it to identify contradictions, leading indicator gaps, and directional mismatches. Returns a `ConflictRecord` list.
- **Confidence aggregation (rule-based):** weighted average of per-agent confidence scores, penalised by `-0.1` per conflict and `-0.05` per freshness mismatch.

Output: a `CanonicalAlignedState` dict written to Supabase `aligned_states` and returned to the synthesis layer. Estimated effort: 3–4 days.

**5. LLM upgrades — `config/models.py`**
- Router: `gemma2:2b` → `gemma3:4b` (same Ollama host, `ollama pull gemma3:4b`)
- Synthesis: `deepseek-r1:8b` → `deepseek-r1:14b` (requires A10G / A100 GPU with headroom)
- No code changes beyond model name constants. Estimated effort: 1 day including GPU validation.

**6. `/align` endpoint — `src/api/routes.py`**
New `POST /align` endpoint that runs Signal Engine + Alignment Coordinator and returns the `CanonicalAlignedState`. The existing `POST /query` endpoint is updated to call `/align` internally before synthesis rather than the previous context assembly function. No breaking change — `/query` response shape is unchanged; the aligned state is an addition to the response payload. Estimated effort: 1 day.

**7. Aligned states table — Supabase migration**
Add `aligned_states` table (schema in Engineering Specifications). Each alignment run is written with a version integer and state hash for deduplication. Enables diffs, audit, and the Alignment Inspector page. Estimated effort: 1 day.

**8. Supabase Realtime push**
When a new aligned state is written, broadcast to the `aligned_states` Realtime channel. The dashboard subscribes and updates the confidence badge without polling. Estimated effort: 1 day.

---

**What changes in the frontend (`csignals/terminal/`):**

**9. Dashboard confidence badge — `/dashboard`**
Add a small aligned state confidence indicator to the existing dashboard header. Shows aggregate confidence score, number of agents active, and a link to the Alignment Inspector. Reads from Supabase Realtime. Estimated effort: 1 day.

**10. Alignment Inspector page — `/alignment` (new page)**
New Next.js page. Displays the current `CanonicalAlignedState` in a readable layout:
- Agent cards: domain, source, fetched_at, freshness status, confidence score
- Conflict panel: each `ConflictRecord` with agent names, type, and description
- Aggregate confidence gauge
- Version history: list of prior aligned states with timestamps (links to diff view)

This is the most important v2.5 deliverable. It is what makes the alignment story visible to a client in a demo. Estimated effort: 3–4 days.

**11. Source registry settings page — `/settings` (new page)**
New Next.js page. Lists all entries in `source_registry` for the tenant. Allows toggling agents active/inactive, viewing freshness TTLs and confidence defaults, and (in a later pass) adding custom connectors. Estimated effort: 2–3 days.

---

### v2.5 Effort Summary

| Change | File(s) | Effort |
|---|---|---|
| Parallel agent fetch | `executor.py` | 1 day |
| AgentOutput schema + BaseAgent | `agents/base.py` (new) | 2 days |
| Source registry table | Supabase migration | 1 day |
| Alignment Coordinator | `alignment.py` (new) | 3–4 days |
| LLM upgrades (gemma3:4b, deepseek-r1:14b) | `config/models.py` + Ollama | 1 day |
| `/align` endpoint + `/query` update | `routes.py` | 1 day |
| Aligned states table | Supabase migration | 1 day |
| Supabase Realtime push | `routes.py` + Supabase config | 1 day |
| Dashboard confidence badge | `terminal/app/dashboard` | 1 day |
| Alignment Inspector page | `terminal/app/alignment` (new) | 3–4 days |
| Settings page | `terminal/app/settings` (new) | 2–3 days |
| **Total** | | **~17–20 days** |

No new infrastructure. No breaking changes. No migration of existing tenants required.

---

### What v2.5 Explicitly Defers

These are v3 items. They do not belong in v2.5 regardless of how tempting they look during the sprint:

| Deferred | Reason |
|---|---|
| LangGraph | asyncio + a structured function is sufficient; LangGraph adds dependency weight with no v2.5 benefit |
| Redis + Celery | Cron-scheduled brief already works; task queue adds ops overhead before it's needed |
| Neo4j / Graph Memory (Module D) | Requires building entity schema and client data curation — a separate project |
| Governor (Module B) | Create the Supabase table as a placeholder; build the logic when a regulated client requires it |
| Vector Memory for conversation history (Module C) | Infrastructure exists; requires session management that isn't in v2 — borderline, defer unless a client asks |
| Modules D, E, F | All require new infrastructure, client data work, or significant frontend builds |
| LLM-based conflict detection escalation | The single `gemma3:4b` call is sufficient; multi-step LangGraph conflict resolution is v3 |

---

### v2.5 → v3 Transition

v2.5 produces a `CanonicalAlignedState` stored in Supabase. That object is the stable interface v3 modules plug into. When v3 development begins:

- **Module B (Governor)** reads from `aligned_states` and wraps the synthesis call — no changes to the alignment layer
- **Module C (Vector Memory)** indexes `aligned_states` content into `vector_memory` — additive, no changes below
- **Module D (Graph Memory)** adds a Neo4j layer that the iSLM router can dispatch to — additive, new route only
- **Module E (Decision Memory)** writes to `decision_memory` after artifact generation — additive, no changes to alignment

v2.5 is the foundation. Every v3 module is additive to it.

## Future Architecture — ADP 8.5: Zero-Knowledge Proofs

This section documents a planned future integration between the Signals platform and ALI's Alignment Delegation Protocol (ADP). It is recorded here to preserve the design intent and prevent it from being lost across context resets.

### Context

ADP is ALI's intra-model deception detection system (see `/adp/`). It operates at the level of a single LLM forward pass, monitoring Hallucination-Associated Neurons (H-Neurons) via CETT streaming hooks. When Tier 1 suppression fails, ADP routes to a Proof-of-Knowledge (PoK) external oracle as a Tier 2 fallback.

**ADP 8.5** is the planned cryptographic verification upgrade to Tier 2:

> When Tier 2 routes to a PoK oracle, the oracle generates a **zero-knowledge proof** (Halo2 or Groth16) certifying that the output token sequence was derived strictly from the verified, bounded knowledge base without parameter drift. This establishes a mathematical guarantee of output state integrity at the delegation boundary, eliminating oracle spoofing and parameter-drift vulnerabilities.

### Why It Matters for the Signals Platform

Signals v3 and MAS produce a `CanonicalAlignedState` — a versioned, attributed, conflict-resolved data object. That object drives downstream decisions, payments (MAS), and artifacts. The trust question at the settlement boundary is not only "did the right agents contribute?" (MAS' job) but also "were those agents' individual outputs generated with integrity?" (ADP's job).

ADP 8.5 closes the integrity gap at the agent-output level with a cryptographic proof that is:
- Verifiable without re-running inference
- Anchored to the specific bounded knowledge base the agent was authorized to use
- Resistant to oracle spoofing (the ZKP is generated by the oracle, not claimed by the agent)

### Integration Point — Arc L1 Settlement

The convergence point between ADP 8.5 and the Signals platform is Arc L1 settlement in **MAS**:

```text
Agent output generated
        ↓
ADP Tier 2 (PoK oracle) routes when H-Neuron risk exceeds threshold
        ↓
Oracle generates ZKP (Halo2/Groth16):
  PROOF: output token sequence ← verified bounded knowledge base, no parameter drift
        ↓
MAS Alignment Coordinator receives:
  - AgentOutput (identity, domain, confidence, data)
  - ZKP attestation (cryptographic integrity certificate)
        ↓
Canonical Aligned State includes ZKP reference per agent (optional field: zkp_attestation_hash)
        ↓
Arc L1 settlement anchors:
  - Canonical Aligned State hash (MAS provenance)
  - ZKP attestation hash (ADP cryptographic integrity)
        ↓
Immutable on-chain receipt:
  identity + provenance + alignment quality + cryptographic output integrity
```

### Implementation Notes

**What must be true before ADP 8.5 integration can begin:**
- Arc L1 mainnet live (planned summer 2026)
- ADP 8.5 ZKP module implemented in the ADP codebase
- MAS MVP deployed with Canonical Aligned State and Arc settlement working

**What MAS must not do (to stay integration-ready):**
- Do not hardcode `aligned_states.agents` as a closed schema — leave the `AgentOutput` struct extensible so `zkp_attestation_hash` can be added as an optional field
- Do not assume agent outputs are integrity-verified at the coordination layer — the Alignment Coordinator handles alignment quality, not cryptographic proof; those are separate concerns

**Why ADP and MAS remain separate codebases:**
ADP requires direct PyTorch forward hooks on model internals (CETT monitor attaches to FFN layer activations). MAS uses Ollama, which abstracts away model internals — there is no hook surface. ADP 8.5 integration at settlement does not require the same infrastructure: the ZKP output is a portable artifact that can be attached to an `AgentOutput` before it reaches the Alignment Coordinator, regardless of how the proof was generated.

**ZKP library selection:** Halo2 (Zcash) preferred for its transparent setup (no trusted ceremony required). Groth16 as fallback if proof generation latency under Halo2 is incompatible with MAS real-time settlement targets.

**Dr. Reza Nourmohammadi** (ADP Co-Investigator, UBC — PhD in ZKP and privacy-preserving federated learning) is the internal lead on ADP 8.5 implementation.

### Status

ADP 8.5 is a planned upgrade path. It is not in the v2.5 scope. It is not a dependency for MAS MVP. It is the intended v3 integration that makes the Signals platform's provenance chain cryptographically complete.

---

## Design Goal

Signals v3 should become the enterprise data alignment and signal-intelligence layer that automates the reconciliation of fragmented internal and external data into a coherent, attributed, conflict-resolved picture — so that downstream decision-making, dashboards, briefs, workflows, and watchlists are built on a trustworthy foundation rather than on manually assembled context.
