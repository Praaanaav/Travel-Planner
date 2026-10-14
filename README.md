
<h1 align="center">✈️ TripPilot</h1>

<p align="center">
  <strong>A multi-agent AI travel planner powered by LangGraph, Groq, and live travel APIs.</strong><br>
  Describe your dream trip in plain English — get flights, hotels, weather, and a full itinerary in under 90 seconds.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-active-22c55e.svg" alt="Status">
  <img src="https://img.shields.io/badge/python-3.11-3b82f6.svg" alt="Python 3.11">
  <img src="https://img.shields.io/badge/framework-FastAPI-009688.svg" alt="FastAPI">
  <img src="https://img.shields.io/badge/agents-LangGraph-6366f1.svg" alt="LangGraph">
  <img src="https://img.shields.io/badge/LLM-Groq-f59e0b.svg" alt="Groq">
  <img src="https://img.shields.io/badge/license-GPL--3.0-blue.svg" alt="License">
  <a href="https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/issues">
    <img src="https://img.shields.io/github/issues/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner.svg" alt="Issues">
  </a>
  <a href="https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/pulls">
    <img src="https://img.shields.io/github/issues-pr/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner.svg" alt="PRs">
  </a>
</p>

## 📸 Demo & Screenshots

> **Live:** Run `uv run python app.py` then open **http://127.0.0.1:8000**

|           Home Screen           |                           Trip Results                           |
| :------------------------------: | :---------------------------------------------------------------: |
| Enter your trip in plain English | Tabbed results: Plan · Itinerary · Flights · Hotels · Weather |

**Example prompts to try:**

> *"Plan a 10-day Europe trip from India in April, mid-range budget for 2 people"*

> *"Budget honeymoon to Bali for 7 days from Mumbai in December"*

---

## 📝 Table of Contents

- [Overview](#-overview)
- [Problem Statement &amp; Solution](#-problem-statement--solution)
- [Key Features](#-key-features)
- [Tech Stack](#-tech-stack)
- [System Architecture](#-system-architecture)
- [How It Works — Agent Workflow](#-how-it-works--agent-workflow)
- [Project Structure](#-project-structure)
- [Prerequisites](#-prerequisites)
- [Installation &amp; Setup](#-installation--setup)
- [Environment Variables](#-environment-variables)
- [Usage](#-usage)
- [API Documentation](#-api-documentation)
- [Example Output](#-example-output)
- [Troubleshooting](#-troubleshooting)
- [Contributing](#-contributing)
- [Acknowledgements](#-acknowledgements)
- [Author &amp; Contact](#-author--contact)

---

## 🧐 Overview

**TripPilot** is a full-stack, multi-agent AI travel planner that turns a single natural-language request into a complete, actionable travel plan. A team of specialised AI agents — each with access to live data APIs — research and compile your trip, then a final agent assembles everything into a clean, structured report.

| Agent                          | What it delivers                                                                    |
| ------------------------------ | ----------------------------------------------------------------------------------- |
| ✈️ **Flight Agent**    | Departure/arrival airports, airlines, typical duration, fare ranges, booking advice |
| 🏨 **Hotel Agent**       | Accommodation options matched to your destination and budget via Tavily search      |
| 🌤️ **Weather Agent**   | Current conditions, multi-day forecast, and weather-based travel advice             |
| 🗺️ **Itinerary Agent** | A realistic, day-by-day schedule combining all gathered data                        |
| 📋 **Final Agent**       | A beautifully formatted, consolidated travel report with budget breakdown           |

Trips are persisted to PostgreSQL via LangGraph's checkpointing — reopen a conversation later and ask follow-up questions without starting over.

---

## ❗ Problem Statement & Solution

### The Problem

Planning a trip means juggling half a dozen browser tabs — one for flights, one for hotels, one for weather, one for local tips — then manually stitching it all into a coherent plan. This takes hours and still produces inconsistent, unstructured results.

### The Solution

TripPilot collapses that research pipeline into a **single conversational prompt**. A multi-agent system dispatches specialised agents to query live APIs, then synthesises their findings into one structured, readable report — in 30–90 seconds.

You describe the trip the way you'd describe it to a friend, and TripPilot handles the rest.

---

## ✨ Key Features

- 🧠 **Multi-agent orchestration** via LangGraph — each specialist agent has a clear, scoped role
- 💬 **Natural language input** — no forms, no dropdowns, just plain English
- ✈️ **Live flight data** via AviationStack MCP (airports, airlines, routes)
- 🏨 **Live hotel search** via Tavily MCP
- 🌦️ **Live weather data** via OpenWeatherMap MCP (current + 5-day forecast)
- 📅 **Day-by-day itinerary generation** from combined research context
- 💰 **Estimated budget breakdown** per trip
- 🗂️ **Trip history** — PostgreSQL-backed conversation threads with full context recall
- 📤 **Export options** — copy, download as Markdown, or print any plan
- 🛠️ **Trip Builder UI** — form-based prompt composer for structured input
- 🌙 **Light / Dark mode** toggle
- ⚡ **Redis caching** — API responses cached to minimise repeated calls and respect rate limits
- 🔐 **Per-session credentials** — users can supply their own API keys via the Settings panel

---

## 🛠️ Tech Stack

| Layer                      | Technology                                                                                     |
| -------------------------- | ---------------------------------------------------------------------------------------------- |
| **Backend**          | [FastAPI](https://fastapi.tiangolo.com/) + [Uvicorn](https://www.uvicorn.org/)                   |
| **AI Orchestration** | [LangGraph](https://langchain-ai.github.io/langgraph/) (StateGraph)                             |
| **LLM**              | [Groq](https://groq.com/) (`langchain-groq`) — fast inference                                |
| **Agent Tools**      | [MCP (Model Context Protocol)](https://modelcontextprotocol.io/) via `langchain-mcp-adapters` |
| **Flight Data**      | [AviationStack](https://aviationstack.com/) (local stdio MCP server)                            |
| **Hotel Search**     | [Tavily](https://tavily.com/) (remote HTTP MCP server)                                          |
| **Weather Data**     | [OpenWeatherMap](https://openweathermap.org/api) (local weather MCP server)                     |
| **Persistence**      | [PostgreSQL](https://www.postgresql.org/) + `langgraph-checkpoint-postgres`                   |
| **Caching**          | [Redis](https://redis.io/) (optional, graceful fallback if unavailable)                         |
| **Frontend**         | HTML + Vanilla JS + Jinja2 Templates                                                           |
| **Package Manager**  | [uv](https://docs.astral.sh/uv/)                                                                |
| **Runtime**          | Python 3.11                                                                                    |

---

## 🏗️ System Architecture

```
                   ┌──────────────────────────────────┐
                   │           Browser / UI            │
                   │      (Jinja2 + Vanilla JS)        │
                   └───────────────┬──────────────────┘
                                   │ HTTP REST
                   ┌───────────────▼──────────────────┐
                   │        FastAPI Application        │
                   │  POST /api/travel · GET /health   │
                   └───────────────┬──────────────────┘
                                   │
                   ┌───────────────▼──────────────────┐
                   │    LangGraph StateGraph Runner    │
                   │       (src/graph/runner.py)       │
                   └──┬──────────┬──────────┬──────────┘
                      │          │          │
            ┌─────────▼───┐ ┌────▼────┐ ┌──▼──────────┐
            │Flight Agent │ │  Hotel  │ │Weather Agent│
            └──────┬──────┘ │  Agent  │ └──────┬──────┘
                   │        └────┬────┘         │
       ┌───────────▼──┐  ┌───────▼───┐  ┌───────▼──────┐
       │AviationStack │  │  Tavily   │  │ OpenWeather  │
       │  MCP (stdio) │  │MCP (HTTP) │  │     MCP      │
       └──────────────┘  └───────────┘  └──────────────┘
                      │          │          │
                   ┌──▼──────────▼──────────▼──────────┐
                   │         Itinerary Agent            │
                   └───────────────┬──────────────────┘
                                   │
                   ┌───────────────▼──────────────────┐
                   │           Final Agent             │
                   │  (assembles full travel report)   │
                   └───────────────┬──────────────────┘
                                   │
            ┌──────────────────────▼──────────────────────┐
            │  PostgreSQL (LangGraph Checkpointer)         │
            │  + Redis Cache (API response TTL)            │
            └─────────────────────────────────────────────┘
```

---

## 🔄 How It Works — Agent Workflow

The LangGraph `StateGraph` runs agents as a **sequential pipeline**, each enriching a shared `TravelState` object:

```
START
  │
  ▼
┌──────────────┐
│ Flight Agent │  → AviationStack MCP: list_airports, list_airlines
│              │  → LLM synthesises: airports, airlines, duration, fare range, tips
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Hotel Agent  │  → Tavily MCP: web search for hotels at destination
│              │  → LLM formats accommodation options and recommendations
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Weather Agent│  → OpenWeather MCP: current weather + 5-day forecast
│              │  → LLM adds weather-based travel advice
└──────┬───────┘
       │
       ▼
┌──────────────────┐
│ Itinerary Agent  │  → Receives flight + hotel + weather context
│                  │  → LLM generates practical day-by-day schedule
└──────┬───────────┘
       │
       ▼
┌─────────────┐
│ Final Agent │  → Aggregates all outputs into a 7-section report:
│             │     Trip Summary · Flights · Hotels · Weather
│             │     Day-by-Day Plan · Budget · Recommendations
└──────┬──────┘
       │
      END → FastAPI returns JSON → Browser renders tabbed UI
```

**Shared `TravelState` passed between all agents:**

| Field               | Type                 | Description                        |
| ------------------- | -------------------- | ---------------------------------- |
| `user_query`      | `str`              | Original natural-language request  |
| `messages`        | `list[AnyMessage]` | Conversation history (append-only) |
| `flight_results`  | `str`              | Output from Flight Agent           |
| `hotel_results`   | `str`              | Output from Hotel Agent            |
| `weather_results` | `str`              | Output from Weather Agent          |
| `itinerary`       | `str`              | Output from Itinerary Agent        |
| `llm_calls`       | `int`              | Running count of LLM invocations   |

---

## 📁 Project Structure

```
TripPilot-Multi-Agent-AI-Travel-Planner/
│
├── app.py                        # FastAPI app — routes, session handling, CORS
├── pyproject.toml                # Project metadata and dependencies (uv)
├── requirements.txt              # pip-compatible dependency list
├── .env                          # Local secrets (gitignored — never commit)
├── .python-version               # Pinned Python version (3.11)
│
├── frontend/
│   ├── templates/
│   │   └── index.html            # Main Jinja2 template (single-page UI)
│   └── static/                   # CSS, JS, icons, assets
│
└── src/
    ├── agents/
    │   ├── destination.py        # Extracts destination from free-text query
    │   ├── flight_agent.py       # Flights: AviationStack MCP + Groq LLM
    │   ├── hotel_agent.py        # Hotels: Tavily MCP + Groq LLM
    │   ├── weather_agent.py      # Weather: OpenWeather MCP + Groq LLM
    │   ├── itinerary_agent.py    # Synthesises all results into a schedule
    │   ├── final_agent.py        # Final report formatter
    │   └── prompts.py            # All prompt templates (single source of truth)
    │
    ├── graph/
    │   ├── state.py              # TravelState TypedDict definition
    │   ├── graph.py              # StateGraph node/edge wiring
    │   └── runner.py             # Graph executor called by FastAPI
    │
    ├── mcp_servers/
    │   ├── config.py             # MCP server configuration
    │   ├── local.py              # AviationStack stdio MCP client
    │   ├── remote.py             # Tavily HTTP MCP client
    │   ├── weather_server.py     # OpenWeather MCP server
    │   └── diagnostics.py        # MCP connectivity health checks
    │
    ├── api/
    │   ├── sessions.py           # Server-side session store (in-memory)
    │   └── validation.py         # Groq key + database URL validators
    │
    ├── clients/
    │   ├── llm.py                # Groq LLM client factory
    │   ├── checkpointer.py       # PostgreSQL LangGraph checkpointer
    │   └── cache.py              # Redis cache client (optional)
    │
    ├── config/
    │   ├── settings.py           # App-wide settings from environment
    │   └── session.py            # Per-request credential context manager
    │
    └── utils/
        └── async_utils.py        # Async helpers, LLM call counter, truncation
```

---

## ✅ Prerequisites

- **Python 3.11** — check with `python --version`
- **[uv](https://docs.astral.sh/uv/)** — project package manager

  ```bash
  pip install uv
  ```
- **A PostgreSQL database** — a free [Render](https://render.com/) Postgres instance works perfectly
- **API keys** from the services below (all have free tiers):

  | Service                                         |        Free Tier        | Sign Up            |
  | ----------------------------------------------- | :---------------------: | ------------------ |
  | [Groq](https://console.groq.com/)                | ✅ Generous free quota | console.groq.com   |
  | [Tavily](https://tavily.com/)                    | ✅ 1,000 searches/month | tavily.com         |
  | [AviationStack](https://aviationstack.com/)      |  ✅ 500 requests/month  | aviationstack.com  |
  | [OpenWeatherMap](https://openweathermap.org/api) |     ✅ 60 calls/min     | openweathermap.org |
- **Redis** *(optional)* — only for caching; the app works fine without it

---

## 🚀 Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/Praaanaav/Travel-Planner
cd Travel-Planner
```

### 2. Install dependencies

```bash
uv sync
```

> Reads `pyproject.toml` and installs everything into a managed virtual environment automatically.

### 3. Configure environment variables

```bash
# Linux / macOS
cp .env.example .env

# Windows
copy .env.example .env
```

Open `.env` and fill in your API keys — see [Environment Variables](#-environment-variables) below.

### 4. Run the app

```bash
uv run python app.py
```

Open **http://127.0.0.1:8000** in your browser. A green *API connected* indicator in the sidebar means you're ready to plan.

---

## 🔑 Environment Variables

### `.env` template

```dotenv
# ── LLM ──────────────────────────────────────────────────────────────────────
GROQ_API_KEY=gsk_your_groq_key_here
GROQ_MODEL=openai/gpt-oss-20b          # Optional — change model without editing code

# ── MCP Servers ───────────────────────────────────────────────────────────────
TAVILY_API_KEY=tvly-your_tavily_key_here
AVIATIONSTACK_API_KEY=your_aviationstack_key
OPENWEATHER_API_KEY=your_openweather_key

# ── Database (LangGraph checkpointer) ─────────────────────────────────────────
DATABASE_URL=postgresql://user:password@host:5432/dbname

# ── Redis Cache (optional) ────────────────────────────────────────────────────
REDIS_URL=redis://localhost:6379/0
CACHE_ENABLED=true

# Optional cache TTL overrides (seconds)
# CACHE_TTL_AIRPORTS=604800
# CACHE_TTL_AIRLINES=604800
# CACHE_TTL_HOTELS=21600
# CACHE_TTL_FORECAST=3600
# CACHE_TTL_WEATHER=600
# CACHE_TTL_DESTINATION=86400

# ── Server ────────────────────────────────────────────────────────────────────
HOST=127.0.0.1
PORT=8000
RELOAD=true

# ── Observability (optional — Langfuse) ───────────────────────────────────────
LANGFUSE_SECRET_KEY=
LANGFUSE_PUBLIC_KEY=
LANGFUSE_BASE_URL=
```

### Variable Reference

| Variable                  | Required | Purpose                                                   |
| ------------------------- | :------: | --------------------------------------------------------- |
| `GROQ_API_KEY`          |    ✅    | Powers all AI agents (LLM inference via Groq)             |
| `DATABASE_URL`          |    ✅    | PostgreSQL connection string — persists trip history     |
| `TAVILY_API_KEY`        |    ✅    | Hotel search and web research                             |
| `AVIATIONSTACK_API_KEY` |    ✅    | Airport and airline data                                  |
| `OPENWEATHER_API_KEY`   |    ✅    | Current weather and forecasts                             |
| `GROQ_MODEL`            |    ❌    | Override the default Groq model                           |
| `REDIS_URL`             |    ❌    | Redis connection for API response caching                 |
| `CACHE_ENABLED`         |    ❌    | Set `false` to disable caching entirely                 |
| `HOST` / `PORT`       |    ❌    | Server bind address (default: `127.0.0.1:8000`)         |
| `RELOAD`                |    ❌    | Auto-reload on file changes (dev only, default: `true`) |
| `LANGFUSE_*`            |    ❌    | LLM observability via Langfuse                            |

> ⚠️ `.env` is listed in `.gitignore`. **Never commit API keys to version control.**

---

## 🎈 Usage

### Planning a trip

Type into the box at the bottom of the screen and press **Enter**:

```
Plan a 10-day Europe trip from India in April, mid-range budget
```

```
Relaxed 5-day trip to Rome and Florence in September for two people
```

```
Budget honeymoon to Bali for 7 days departing from Mumbai in December
```

Not sure where to start? Click one of the **suggestion cards** on the home screen.

A plan takes **30–90 seconds** to build. Each agent stage appears as it progresses.

### Trip Builder (structured input)

Click the **⚙️ sliders icon** next to the message box to open the structured form. Fill in:

- Origin city / airport
- Destination
- Travel dates and duration
- Number of travellers
- Budget level
- Interests / travel style

Press **Write my prompt** — TripPilot composes the natural-language request for you, ready to send or edit.

### Reading your plan

Results are split into tabs so you can jump straight to what you need:

| Tab                 | Contents                                          |
| ------------------- | ------------------------------------------------- |
| **Plan**      | Full AI-generated narrative summary               |
| **Itinerary** | Day-by-day schedule                               |
| **Flights**   | Airports, airlines, durations, fare estimates     |
| **Hotels**    | Accommodation options and recommendations         |
| **Weather**   | Conditions, forecast, weather-based travel advice |

### Saving, exporting and revisiting

- Every trip **auto-saves** to the sidebar — click any entry to reopen it
- Ask **follow-up questions** on an open trip — full context is remembered
- Use icons at the top of a result: **📋 Copy** · **⬇️ Download Markdown** · **🖨️ Print**
- **New trip** resets the conversation
- **☀️ / 🌙** toggles light / dark mode

---

---

## 📋 Example Output

**Input prompt:**

> *"Plan a 10-day Europe trip from India in April, mid-range budget"*

**Condensed output:**

```
✈️ Trip Summary
───────────────
A 10-day European adventure from India in April.
Route: Mumbai (BOM) → Paris (CDG) → Rome (FCO) → Mumbai.

✈️ Flight Information
──────────────────────
Departure:  Chhatrapati Shivaji Maharaj International (BOM)
Arrival:    Paris Charles de Gaulle (CDG)
Airlines:   Air France, IndiGo, Emirates (via Dubai)
Duration:   9–12 hours with layovers
Fare range: ₹45,000 – ₹85,000 per person (return)
⚠️  April is peak season — book 3+ months ahead

🏨 Hotel Suggestions
─────────────────────
Paris → Ibis Paris Tour Eiffel     ~₹5,500/night  ⭐⭐⭐
Rome  → Hotel Artemide             ~₹7,200/night  ⭐⭐⭐⭐

🌤️ Weather
──────────
Paris in April: 12–18°C, mild showers — pack a light rain jacket.
Rome in April:  16–22°C, mostly sunny — ideal sightseeing weather.

🗓️ Day-by-Day Itinerary
─────────────────────────
Day 1–2   Arrive Paris. Eiffel Tower, Seine River cruise, Louvre.
Day 3–4   Versailles day trip. Montmartre. Local bistro dinner.
Day 5     Train to Rome (Frecciarossa, ~4 h). Trastevere evening.
Day 6–7   Colosseum, Vatican Museums, St Peter's Basilica.
Day 8–9   Day trip to Florence — Uffizi Gallery, Ponte Vecchio.
Day 10    Return flight from Rome (FCO).

💰 Estimated Budget (per person)
──────────────────────────────────
Category              Estimate
─────────────────     ─────────
Flights               ₹60,000
Hotels (9 nights)     ₹56,000
Food & dining         ₹18,000
Transport (trains)    ₹ 8,000
Sightseeing & entry   ₹10,000
─────────────────     ─────────
Total                 ~₹1,52,000

📌 Final Recommendations
─────────────────────────
• Book flights and hotels at least 8–10 weeks before April.
• Apply for a Schengen visa early — processing takes 3–4 weeks.
• Carry an EU travel adapter. Most restaurants accept card payments.
```

---

## 🎉 Acknowledgements

- [Groq](https://groq.com/) — ultra-fast LLM inference that makes sub-90-second planning possible
- [LangChain](https://langchain.com/) & [LangGraph](https://langchain-ai.github.io/langgraph/) — the agent orchestration backbone
- [Tavily](https://tavily.com/) — AI-optimised web search for hotel results
- [AviationStack](https://aviationstack.com/) — global flight and airline data
- [OpenWeatherMap](https://openweathermap.org/) — reliable weather and forecast API
- [MCP (Model Context Protocol)](https://modelcontextprotocol.io/) — structured tool interface for agents

---

## 👤 Author

**Pranav Kumar Swain**
