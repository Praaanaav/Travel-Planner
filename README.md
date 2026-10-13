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
    <img src="https://img.shields.io/github/issues/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner.svg" alt="GitHub Issues">
  </a>
  <a href="https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/pulls">
    <img src="https://img.shields.io/github/issues-pr/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner.svg" alt="GitHub PRs">
  </a>
</p>

---

## 📸 Demo & Screenshots

> **Live:** Open `http://127.0.0.1:8000` after running `uv run python app.py`

|           Home Screen           |                           Trip Results                           |
| :------------------------------: | :---------------------------------------------------------------: |
| Enter your trip in plain English | Tabbed results: Plan · Itinerary · Flights · Hotels · Weather |

> **Example prompt:** *"Plan a 10-day Europe trip from India in April, mid-range budget for 2 people"*

---

## 📝 Table of Contents

- [Overview](#overview)
- [Problem Statement &amp; Solution](#problem-statement--solution)
- [Key Features](#key-features)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [How It Works](#how-it-works--agent-workflow)
- [Project Structure](#project-structure)
- [Prerequisites](#prerequisites)
- [Installation &amp; Setup](#installation--setup)
- [Environment Variables](#environment-variables)
- [Usage](#usage)
- [API Documentation](#api-documentation)
- [Example Output](#example-output)
- [Author &amp; Contact](#author--contact)

---

## 🧐 Overview

**TripPilot** is a full-stack, multi-agent AI travel planner that turns a single natural-language request into a complete, actionable travel plan. Under the hood, a team of specialised AI agents — each with access to live data APIs — research and compile your trip in parallel, then a final agent assembles everything into a clean, structured report.

| Component                     | What it delivers                                                                    |
| ----------------------------- | ----------------------------------------------------------------------------------- |
| ✈️**Flight Agent**    | Departure/arrival airports, airlines, typical duration, fare ranges, booking advice |
| 🏨**Hotel Agent**       | Accommodation options matched to your destination and budget via Tavily search      |
| 🌤️**Weather Agent**   | Current conditions, multi-day forecast, and weather-based travel advice             |
| 🗺️**Itinerary Agent** | A realistic, day-by-day schedule combining all gathered data                        |
| 📋**Final Agent**       | A beautifully formatted, consolidated travel report with budget breakdown           |

Trips are persisted to PostgreSQL via LangGraph's checkpointing — so you can reopen a conversation later and ask follow-up questions without starting over.

---

## ❗ Problem Statement & Solution

### The Problem

Planning a trip means juggling half a dozen browser tabs — one for flights, one for hotels, one for weather, one for local tips — then manually stitching it all together into a coherent plan. This takes hours and still produces inconsistent, unstructured results.

### The Solution

TripPilot collapses that research pipeline into a **single conversational prompt**. A supervisor-style multi-agent system dispatches specialised agents to query live APIs simultaneously, then synthesises their findings into one structured, readable report — in 30–90 seconds.

You describe the trip the way you'd describe it to a friend, and TripPilot handles the rest.

---

## ✨ Key Features

- 🧠 **Multi-agent orchestration** via LangGraph — each specialist agent has a clear, scoped role
- 💬 **Natural language input** — no forms, no dropdowns, just plain English
- ✈️ **Live flight data** via AviationStack MCP (airports, airlines, routes)
- 🏨 **Live hotel search** via Tavily MCP
- 🌦️ **Live weather data** via OpenWeatherMap MCP (current + forecast)
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
| **Caching**          | [Redis](https://redis.io/) (optional, graceful fallback)                                        |
| **Frontend**         | HTML + Vanilla JS + Jinja2 Templates                                                           |
| **Package Manager**  | [uv](https://docs.astral.sh/uv/)                                                                |
| **Runtime**          | Python 3.11                                                                                    |

---

## 🏗️ System Architecture

```
                        ┌─────────────────────────────────────────┐
                        │              Browser / UI               │
                        │         (Jinja2 + Vanilla JS)           │
                        └────────────────┬────────────────────────┘
                                         │ HTTP (REST)
                        ┌────────────────▼────────────────────────┐
                        │          FastAPI Application            │
                        │   POST /api/travel  ·  GET /api/config  │
                        └────────────────┬────────────────────────┘
                                         │
                        ┌────────────────▼────────────────────────┐
                        │       LangGraph StateGraph Runner       │
                        │         (src/graph/runner.py)           │
                        └──┬─────────┬────────┬─────────┬─────────┘
                           │         │        │         │
               ┌───────────▼─┐ ┌─────▼──┐ ┌──▼──────┐ ┌▼────────────┐
               │ Flight Agent│ │ Hotel  │ │ Weather │ │ Itinerary   │
               │             │ │ Agent  │ │ Agent   │ │ Agent       │
               └──────┬──────┘ └───┬────┘ └──┬──────┘ └──────┬──────┘
                      │            │          │               │
          ┌───────────▼──┐  ┌──────▼──┐ ┌────▼────────┐      │
          │ AviationStack│  │ Tavily  │ │ OpenWeather │      │
          │  MCP (stdio) │  │  MCP    │ │   MCP       │      │
          └──────────────┘  │ (HTTP)  │ └─────────────┘      │
                            └─────────┘                       │
                                                              │
                        ┌─────────────────────────────────────▼──┐
                        │           Final Agent                   │
                        │  (assembles report + budget estimate)   │
                        └────────────────┬────────────────────────┘
                                         │
                  ┌──────────────────────▼──────────────────────────┐
                  │          PostgreSQL (LangGraph Checkpointer)     │
                  │         + Redis Cache (API response TTL)         │
                  └─────────────────────────────────────────────────┘
```

---

## 🔄 How It Works — Agent Workflow

The LangGraph `StateGraph` executes agents as a **sequential pipeline**, each agent enriching a shared `TravelState` object:

```
START
  │
  ▼
┌──────────────┐
│ Flight Agent │  Calls AviationStack MCP → list_airports, list_airlines
│              │  LLM synthesises: airports, airlines, duration, fare range, tips
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Hotel Agent  │  Calls Tavily MCP → web search for hotels at destination
│              │  LLM formats accommodation options and recommendations
└──────┬───────┘
       │
       ▼
┌──────────────┐
│ Weather Agent│  Calls OpenWeather MCP → current weather + 5-day forecast
│              │  LLM adds weather-based travel advice
└──────┬───────┘
       │
       ▼
┌────────────────────┐
│ Itinerary Agent    │  Receives flight + hotel + weather results
│                    │  LLM generates a practical day-by-day schedule
└──────┬─────────────┘
       │
       ▼
┌─────────────┐
│ Final Agent │  Aggregates all outputs into a formatted 7-section report:
│             │  Trip Summary · Flights · Hotels · Weather ·
│             │  Day-by-Day Plan · Budget · Recommendations
└──────┬──────┘
       │
      END → result returned to FastAPI → sent to browser
```

**State shared across all agents (`TravelState`):**

| Field               | Type                 | Description                          |
| ------------------- | -------------------- | ------------------------------------ |
| `user_query`      | `str`              | Original natural-language request    |
| `messages`        | `list[AnyMessage]` | Full conversation history (appended) |
| `flight_results`  | `str`              | Output from Flight Agent             |
| `hotel_results`   | `str`              | Output from Hotel Agent              |
| `weather_results` | `str`              | Output from Weather Agent            |
| `itinerary`       | `str`              | Output from Itinerary Agent          |
| `llm_calls`       | `int`              | Running count of LLM invocations     |

---

## 📁 Project Structure

```
TripPilot-Multi-Agent-AI-Travel-Planner/
│
├── app.py                          # FastAPI app — routes, session handling, CORS
├── pyproject.toml                  # Project metadata and dependencies (uv)
├── requirements.txt                # pip-compatible dependency list
├── .env                            # Local secrets (gitignored)
├── .python-version                 # Pinned Python version (3.11)
│
├── frontend/
│   ├── templates/
│   │   └── index.html              # Main Jinja2 template (single-page UI)
│   └── static/                     # CSS, JS, icons
│
└── src/
    ├── agents/
    │   ├── __init__.py             # Agent exports
    │   ├── destination.py          # Extracts destination from free-text query
    │   ├── flight_agent.py         # Flights: AviationStack MCP + Groq LLM
    │   ├── hotel_agent.py          # Hotels: Tavily MCP + Groq LLM
    │   ├── weather_agent.py        # Weather: OpenWeather MCP + Groq LLM
    │   ├── itinerary_agent.py      # Itinerary: synthesises all results
    │   ├── final_agent.py          # Final report formatter
    │   └── prompts.py              # All prompt templates (single source of truth)
    │
    ├── graph/
    │   ├── state.py                # TravelState TypedDict definition
    │   ├── graph.py                # StateGraph wiring (edges and nodes)
    │   └── runner.py               # Graph executor called by FastAPI
    │
    ├── mcp_servers/
    │   ├── config.py               # MCP server configuration
    │   ├── local.py                # AviationStack stdio MCP client
    │   ├── remote.py               # Tavily HTTP MCP client
    │   ├── weather_server.py       # OpenWeather MCP server
    │   └── diagnostics.py          # MCP connectivity health checks
    │
    ├── api/
    │   ├── sessions.py             # Server-side session store (in-memory)
    │   └── validation.py           # Groq key + database URL validators
    │
    ├── clients/
    │   ├── llm.py                  # Groq LLM client factory
    │   ├── checkpointer.py         # PostgreSQL LangGraph checkpointer
    │   └── cache.py                # Redis cache client (optional)
    │
    ├── config/
    │   ├── settings.py             # App-wide settings from environment
    │   └── session.py              # Per-request credential context manager
    │
    └── utils/
        └── async_utils.py          # Async helpers, LLM call counter, truncation
```

---

## ✅ Prerequisites

Before you start, make sure you have:

- **Python 3.11** (`python --version` should show `3.11.x`)
- **[uv](https://docs.astral.sh/uv/)** — the package manager used by this project

  ```bash
  pip install uv
  # or: curl -LsSf https://astral.sh/uv/install.sh | sh
  ```
- **A PostgreSQL database** — a free [Render](https://render.com/) Postgres instance works perfectly
- **API keys** from the following services (all have free tiers):

  | Service                                         | Free Tier               | Get Key                                          |
  | ----------------------------------------------- | ----------------------- | ------------------------------------------------ |
  | [Groq](https://console.groq.com/)                | ✅ Generous free quota  | [console.groq.com](https://console.groq.com/)     |
  | [Tavily](https://tavily.com/)                    | ✅ 1,000 searches/month | [tavily.com](https://tavily.com/)                 |
  | [AviationStack](https://aviationstack.com/)      | ✅ 500 requests/month   | [aviationstack.com](https://aviationstack.com/)   |
  | [OpenWeatherMap](https://openweathermap.org/api) | ✅ 60 calls/min         | [openweathermap.org](https://openweathermap.org/) |
- **Redis** (optional) — only needed for response caching; the app runs fine without it

---

## 🚀 Installation & Setup

### 1. Clone the repository

```bash
git clone https://github.com/Praaanaav/Travel-Planner
cd TripPilot-Multi-Agent-AI-Travel-Planner
```

### 2. Install dependencies

```bash
uv sync
```

> This reads `pyproject.toml` and installs all dependencies into a managed virtual environment automatically.

### 3. Configure environment variables

Copy the example and fill in your keys:

```bash
cp .env.example .env   # Linux/Mac
copy .env.example .env # Windows
```

Then edit `.env` — see the [Environment Variables](#environment-variables) section below.

### 4. Start the app

```bash
uv run python app.py
```

Open **http://127.0.0.1:8000** in your browser. If you see a green *API connected* indicator in the sidebar, you're ready to go.

---

## 🔑 Environment Variables

Create a `.env` file in the project root. Here is the full reference:

```dotenv
# ── LLM ─────────────────────────────────────────────────────────────────────
GROQ_API_KEY=gsk_your_groq_key_here
GROQ_MODEL=openai/gpt-oss-20b        # Optional — change to any model your key supports

# ── MCP Servers ──────────────────────────────────────────────────────────────
TAVILY_API_KEY=tvly-your_tavily_key_here       # Hotel & web search (remote HTTP MCP)
AVIATIONSTACK_API_KEY=your_aviationstack_key   # Flight data (local stdio MCP)
OPENWEATHER_API_KEY=your_openweather_key       # Weather data (local MCP server)

# ── Database (LangGraph checkpointer) ────────────────────────────────────────
DATABASE_URL=postgresql://user:password@host:5432/dbname

# ── Redis Cache (optional) ───────────────────────────────────────────────────
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

# ── Observability (optional) ─────────────────────────────────────────────────
LANGFUSE_SECRET_KEY=
LANGFUSE_PUBLIC_KEY=
LANGFUSE_BASE_URL=
```

### Variable Reference

| Variable                  | Required | Description                                             |
| ------------------------- | :------: | ------------------------------------------------------- |
| `GROQ_API_KEY`          |    ✅    | Powers all AI agents (LLM inference)                    |
| `DATABASE_URL`          |    ✅    | PostgreSQL connection string — saves trip history      |
| `TAVILY_API_KEY`        |    ✅    | Hotel search and general web research                   |
| `AVIATIONSTACK_API_KEY` |    ✅    | Airport and airline lookup                              |
| `OPENWEATHER_API_KEY`   |    ✅    | Current weather and forecasts                           |
| `GROQ_MODEL`            |    ❌    | Override the default Groq model without touching code   |
| `REDIS_URL`             |    ❌    | Redis connection for API response caching               |
| `CACHE_ENABLED`         |    ❌    | Set to`false` to disable caching entirely             |
| `HOST` / `PORT`       |    ❌    | Server bind address (defaults:`127.0.0.1:8000`)       |
| `RELOAD`                |    ❌    | Hot-reload on file changes (default:`true`, dev only) |
| `LANGFUSE_*`            |    ❌    | LLM observability via Langfuse                          |

> ⚠️ Your `.env` file is listed in `.gitignore`. **Never commit API keys to version control.**

---

## 🎈 Usage

### Planning a trip

Type your request into the input box at the bottom and press **Enter**. Natural language works best:

```
Plan a 10-day Europe trip from India in April, mid-range budget
```

```
I want a relaxed 5-day trip to Rome and Florence in September for two people
```

```
Budget honeymoon to Bali for 7 days departing from Mumbai in December
```

Not sure where to start? Click one of the **suggestion cards** on the home screen.

A plan takes **30–90 seconds** to build. You'll see each agent stage as it progresses.

### Using the Trip Builder

Click the **⚙️ sliders icon** next to the message box to open the structured form. Fill in:

- Origin city / airport
- Destination
- Travel dates and duration
- Number of travellers
- Budget level
- Interests / travel style

Press **Write my prompt** — TripPilot composes the request for you, ready to send or edit.

### Reading your plan

Results are split into tabs:

| Tab                 | Contents                                      |
| ------------------- | --------------------------------------------- |
| **Plan**      | Full AI-generated narrative summary           |
| **Itinerary** | Day-by-day schedule                           |
| **Flights**   | Airports, airlines, durations, fare estimates |
| **Hotels**    | Accommodation options and recommendations     |
| **Weather**   | Conditions, forecast, travel advice           |

### Saving, exporting and revisiting

- Every trip is saved to the sidebar automatically — click to reopen
- Ask **follow-up questions** on any open trip and it remembers the full context
- Use the icons at the top of a result to **📋 copy**, **⬇️ download as Markdown**, or **🖨️ print**
- Click **New trip** to start fresh
- Toggle **☀️ / 🌙** at the bottom of the sidebar for light/dark mode

---

## 📡 API Documentation

The FastAPI backend exposes the following REST endpoints.
Interactive API docs are available at **http://127.0.0.1:8000/docs** (Swagger UI) while the server is running.

### `GET /`

Returns the main HTML page (the TripPilot UI).

---

### `GET /api/config`

Returns the current credential status for this session. **Never returns secret values.**

**Response:**

```json
{
  "ready": true,
  "missing": [],
  "groq_configured": true,
  "database_configured": true
}
```

---

### `POST /api/config`

Store per-session credentials. Each value is validated against the real service before being accepted.

**Request body:**

```json
{
  "groq_api_key": "gsk_...",
  "database_url": "postgresql://..."
}
```

**Success response (`200`):**

```json
{ "success": true, "ready": true, "missing": [] }
```

**Validation failure (`400`):**

```json
{
  "success": false,
  "errors": {
    "groq_api_key": "Invalid API key — the Groq service rejected it."
  }
}
```

---

### `DELETE /api/config`

Clear session credentials and fall back to the server's `.env` values.

**Response:**

```json
{ "success": true, "ready": true }
```

---

### `POST /api/travel`

Run the multi-agent travel planner for a given query.

**Request body:**

```json
{
  "message": "Plan a 7-day trip to Tokyo from London in March",
  "thread_id": null
}
```

> Pass `thread_id` from a previous response to continue a conversation with full context.

**Success response (`200`):**

```json
{
  "success": true,
  "thread_id": "a1b2c3d4-...",
  "answer": "# Trip Summary\n...",
  "flight_results": "**Departure Airport:** Heathrow (LHR)...",
  "hotel_results": "**Shinjuku Granbell Hotel** — ★★★★...",
  "weather_results": "Current: 8°C, partly cloudy. Forecast: ...",
  "itinerary": "**Day 1:** Arrive at Narita (NRT)...",
  "llm_calls": 5
}
```

**Missing credentials (`428`):**

```json
{
  "success": false,
  "error": "GROQ_API_KEY is not configured.",
  "missing": ["GROQ_API_KEY"]
}
```

---

### `GET /health`

Health check used by hosting platforms (Render, Railway, etc.).

**Response:**

```json
{
  "status": "ok",
  "message": "AI Travel Planner API is running",
  "ready": true,
  "missing": [],
  "cache": { "enabled": true, "connected": true }
}
```

---

## 📋 Example Output

**Input prompt:**

> *Plan a 10-day Europe trip from India in April, mid-range budget*

**Output (condensed):**

```markdown
## ✈️ Trip Summary
A 10-day European adventure from India in April.
Recommended route: Mumbai (BOM) → Paris (CDG) → Rome (FCO) → Mumbai.

## ✈️ Flight Information
- Departure: Chhatrapati Shivaji Maharaj International (BOM)
- Arrival: Paris Charles de Gaulle (CDG)
- Airlines: Air France, IndiGo, Emirates (via Dubai)
- Estimated duration: 9–12 hours (with layovers)
- Fare estimate: ₹45,000 – ₹85,000 per person
- ⚠️ April is peak season — book 3+ months in advance

## 🏨 Hotel Suggestions
Paris:  Ibis Paris Tour Eiffel — ~₹5,500/night ⭐⭐⭐
Rome:   Hotel Artemide — ~₹7,200/night ⭐⭐⭐⭐

## 🌤️ Weather
Paris in April: 12–18°C, mild showers. Pack a light rain jacket.
Rome in April: 16–22°C, mostly sunny — ideal sightseeing weather.

## 🗓️ Day-by-Day Itinerary
Day 1–2: Arrive Paris. Eiffel Tower, Seine River cruise, Louvre.
Day 3–4: Versailles day trip. Montmartre. Local bistro dinner.
Day 5:   Train to Rome (Frecciarossa, ~4h). Check in. Trastevere evening.
Day 6–7: Colosseum, Vatican Museums, St Peter's Basilica.
Day 8–9: Day trip to Florence — Uffizi Gallery, Ponte Vecchio.
Day 10:  Return flight from Rome (FCO).

## 💰 Estimated Budget (per person)
| Category              | Estimate   |
|-----------------------|------------|
| Flights               | ₹60,000    |
| Hotels (9 nights)     | ₹56,000    |
| Food & dining         | ₹18,000    |
| Transport             | ₹8,000     |
| Sightseeing & entry   | ₹10,000    |
| **Total**             | **~₹1,52,000** |

## 📌 Final Recommendations
- Book flights and hotels at least 8–10 weeks before April.
- Get a Schengen visa well in advance (processing: 3–4 weeks).
- Carry an EU travel adapter.
```

---

## 🔧 Troubleshooting

<details>
<summary><b>The page loads but planning fails</b></summary>

Check the status indicator at the bottom of the sidebar. If it says *API unreachable*, the server has stopped — restart it with `uv run python app.py`. Otherwise, check the terminal for the full error traceback.

</details>

<details>
<summary><b>Error: "Model does not exist"</b></summary>

Groq retires models over time. List models available to your key:

```bash
curl -s https://api.groq.com/openai/v1/models \
  -H "Authorization: Bearer $GROQ_API_KEY"
```

Pick a current model ID and set it as `GROQ_MODEL` in your `.env`.

</details>

<details>
<summary><b>Error: "DATABASE_URL is missing"</b></summary>

The app needs PostgreSQL to persist conversation threads. Add a valid `DATABASE_URL` to your `.env`. A free [Render](https://render.com/) Postgres database works perfectly.

</details>

<details>
<summary><b>Plans take longer than 90 seconds</b></summary>

This is expected on cold runs — agents make live API calls and multiple LLM inferences. Enable Redis caching (`CACHE_ENABLED=true`) to dramatically speed up repeated queries by caching airport/airline data, hotel searches, and weather for configurable TTLs.

</details>

---

## 🤝 Contributing

Contributions are welcome! This project is actively developed.

1. **Fork** the repository and clone your fork
2. Follow [Installation &amp; Setup](#installation--setup) to get running
3. Create a branch: `git checkout -b feature/your-feature-name`
4. Make focused, well-tested changes
5. Never commit your `.env` file or API keys
6. Open a **pull request** against `main` describing what changed and why

For bugs, open an [issue](https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/issues) with: what you did, what you expected, what happened, and terminal output.

---

## 🎉 Acknowledgements

- [Groq](https://groq.com/) — ultra-fast LLM inference
- [LangChain](https://langchain.com/) & [LangGraph](https://langchain-ai.github.io/langgraph/) — agent orchestration
- [Tavily](https://tavily.com/) — AI-optimised web search
- [AviationStack](https://aviationstack.com/) — global flight and airline data
- [OpenWeatherMap](https://openweathermap.org/) — reliable weather and forecast API
- [MCP (Model Context Protocol)](https://modelcontextprotocol.io/) — structured tool interface for agents

---

## 👤 Author & Contact

**Pranav Kumar Swain**

- 🌐 GitHub: [@Praaanaav](https://github.com/Praaanaav)
- 📧 Email: [kumarpranav15404@gmail.com](mailto:kumarpranav15404@gmail.com)
