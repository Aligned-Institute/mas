# Signals v3

Enterprise data alignment platform — multi-agent Signal Engine + Alignment Coordinator + Canonical Aligned State. Python/FastAPI backend + Next.js terminal dashboard.

Base for the MAS platform family. See `signals-design-v3.md` for the full architecture, module specs (A–F), and build roadmap.

---

## Prerequisites

### 1. Ollama (local LLM server)

```bash
brew install ollama
ollama serve
```

Pull the required models:

```bash
ollama pull gemma3:4b          # orchestration — iSLM routing, conflict detection (temp 0.0)
ollama pull deepseek-r1:14b    # synthesis — final answer generation (requires A100 / A10G)
ollama pull nomic-embed-text   # embeddings — RAG retrieval, vector memory
```

Fallbacks if VRAM is limited:
```bash
ollama pull gemma2:2b          # orchestration fallback
ollama pull deepseek-r1:8b     # synthesis fallback (RTX 4090 24GB)
```

### 2. Python 3.11+

### 3. Node.js 20+

---

## Setup

### Backend

```bash
python -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
```

Copy and fill in the env file:

```bash
cp config/.env.example config/.env
```

Required keys in `config/.env`:

| Key | Required | Purpose |
|-----|----------|---------|
| `ANTHROPIC_API_KEY` | Optional | Claude synthesis escalation (opt-in only — never used for orchestration) |
| `FRED_API_KEY` | For macro agent | Free at fred.stlouisfed.org |
| `SUPABASE_URL` / `SUPABASE_KEY` | Required | aligned_states, source_registry, auth, vector memory |

Start the backend:

```bash
bash scripts/start_islm.sh
# or directly:
python -m src.islm.app
```

Backend runs on `http://localhost:8100`.

### Frontend (terminal)

```bash
cd terminal
npm install
```

Create `terminal/.env.local`:

```
SUPABASE_URL=https://your-project.supabase.co
SUPABASE_KEY=your-service-role-key
API_URL=http://localhost:8000
```

```bash
npm run dev
```

Dashboard runs on `http://localhost:3000`.

---

## Architecture

```
signalsv3/
├── src/islm/
│   ├── agents/            # BaseAgent + domain agents (commodity, macro, news, feedstock, etc.)
│   ├── alignment.py       # Alignment Coordinator — conflict detection, freshness, CAS
│   ├── executor.py        # Signal Engine — parallel asyncio.gather fetch
│   ├── router.py          # iSLM routing (gemma3:4b, temp 0.0)
│   ├── app.py             # FastAPI — /align, /query, /state, /sources endpoints
│   ├── config.py          # Model names, URLs, agent list
│   ├── db.py              # Supabase client
│   └── schemas.py         # Pydantic models
├── scripts/               # Supabase migrations, RAG indexing, brief generator
├── terminal/              # Next.js 15 dashboard
│   └── src/app/
│       ├── (protected)/
│       │   ├── dashboard/     # Live signals + confidence badge
│       │   ├── alignment/     # Alignment Inspector — agent cards, conflict panel, CAS history
│       │   ├── settings/      # Source registry management
│       │   ├── sage/          # Sage chat interface
│       │   ├── upload/        # Data Analyzer
│       │   ├── leaderboard/   # Signal Board
│       │   ├── ecosystem/     # Value chain map
│       │   └── portfolio/     # Brief archive
│       └── api/
│           ├── alignment/     # /latest, /history, /[id], /sources
│           └── sage/          # /generate, /chat
├── tests/                 # pytest suite
├── config/                # .env.example (never commit .env)
├── docs/                  # Architecture diagrams, design references
└── signals-design-v3.md   # Full v3 spec — modules A–F, DB schema, API reference, build roadmap
```

The backend routes queries through:
- **rag** — vector search over indexed knowledge corpus
- **mcp** — live data via parallel agent fetch (Signal Engine)
- **hybrid** — both
- **direct** — model answers from context only
- **vector** — Module C semantic memory search *(v3)*
- **graph** — Module D entity relationship query *(v3)*
- **internal** — Module A ERP/procurement connectors *(v3)*

---

## v3 Module Roadmap

| Module | Name | Status |
|--------|------|--------|
| Core | Signal Engine + Alignment Coordinator + CAS | ✅ baseline |
| A | Extended Connectors (ERP, procurement, custom APIs) | 🔲 v3 build |
| B | Governor (access control, lineage, compliance) | 🔲 v3 build |
| C | Vector Memory (conversation history, semantic recall) | 🔲 v3 build |
| D | Graph Memory (Neo4j entity relationships) | 🔲 v3 build |
| E | Decision Memory (outcome tracking, audit trail) | 🔲 v3 build |
| F | Extended Output Factory (watchlists, scheduled briefs) | 🔲 v3 build |

See `signals-design-v3.md` for full specs, SQL schemas, and API endpoints for each module.

---

## Platform Family

```
MAS  ←  domain-agnostic alignment base layer
    │
    ├── cSignals   ←  specialty chemicals vertical (live)
    ├── Signals v3 ←  enterprise intelligence (this repo)
    └── pSignals   ←  payment vertical (Circle/Arc)
```
