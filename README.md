<h1 align="center">✈️ TripPilot</h1>

<p align="center">
  <b>A multi-agent AI travel planner.</b><br>
  Describe your trip in plain English and get flights, hotels, weather and a day-by-day itinerary in about a minute.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/status-in%20progress-f59e0b.svg" alt="Status">
  <img src="https://img.shields.io/badge/python-3.11-3776ab.svg" alt="Python 3.11">
  <a href="https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/issues"><img src="https://img.shields.io/github/issues/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner.svg" alt="GitHub Issues"></a>
  <a href="https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/pulls"><img src="https://img.shields.io/github/issues-pr/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner.svg" alt="GitHub Pull Requests"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-GPL--3.0-blue.svg" alt="License"></a>
</p>

---

## Table of Contents

- [About](#about)
- [Features](#features)
- [Quick Start](#quick-start)
- [Configuration](#configuration)
- [Usage](#usage)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)
- [Authors](#authors)
- [Acknowledgements](#acknowledgements)
- [License](#license)

## About

Planning a trip usually means juggling half a dozen browser tabs: one for flights, one for hotels, one for the weather, and a notes app where you try to put it all in order.

TripPilot replaces that with a single conversation. Tell it what you want the way you'd tell a friend:

> *Plan a 10 day Europe trip from India in April, mid-range budget*

A team of specialised AI agents then researches the trip in parallel areas of focus. One looks at flights, one finds places to stay, and one checks the weather for your dates. Their findings are merged into one actionable plan with a day-by-day schedule and a cost estimate.

## Features

| | Agent | What you get |
| --- | --- | --- |
| ✈️ | **Flights** | Likely airports, airlines on the route, typical duration and fare range |
| 🏨 | **Hotels** | Accommodation options matched to your destination and budget |
| 🌤️ | **Weather** | Current conditions and forecast, with travel advice |
| 🗺️ | **Itinerary** | A realistic day-by-day plan you can follow |
| 💰 | **Budget** | An estimated cost breakdown for the whole trip |

Plans are saved as you go, so you can reopen a trip later and ask follow-up questions without starting over.

## Quick Start

### Prerequisites

- **Python 3.11**
- **[uv](https://docs.astral.sh/uv/)** for dependency management
- **A PostgreSQL database** (a free [Render](https://render.com/) instance works fine)
- **API keys** for the services below, all of which have free tiers:
  - [Groq](https://console.groq.com/)
  - [Tavily](https://tavily.com/)
  - [AviationStack](https://aviationstack.com/)
  - [OpenWeather](https://openweathermap.org/api)

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner.git
cd TripPilot-Multi-Agent-AI-Travel-Planner

# 2. Install dependencies
uv sync
```

### Configuration

Create a `.env` file in the project root:

```dotenv
# Required
GROQ_API_KEY=your_groq_key
DATABASE_URL=postgresql://user:password@host:5432/dbname
TAVILY_API_KEY=your_tavily_key
AVIATIONSTACK_API_KEY=your_aviationstack_key
OPENWEATHER_API_KEY=your_openweather_key

# Optional
GROQ_MODEL=openai/gpt-oss-20b
```

| Variable | Required | Purpose |
| --- | :---: | --- |
| `GROQ_API_KEY` | ✅ | Powers the AI planning |
| `DATABASE_URL` | ✅ | Saves your trips so you can return to them |
| `TAVILY_API_KEY` | ✅ | Hotel search |
| `AVIATIONSTACK_API_KEY` | ✅ | Airport and airline information |
| `OPENWEATHER_API_KEY` | ✅ | Weather and forecasts |
| `GROQ_MODEL` | ❌ | Switch the AI model without editing code |

> [!WARNING]
> `.env` is ignored by Git. Never commit real API keys.

### Run

```bash
uv run python app.py
```

Open **<http://127.0.0.1:8000>**. If you see the planner with a green *API connected* dot at the bottom of the sidebar, you're ready to go.

## Usage

### Plan a trip

Type your request in the box at the bottom and press **Enter**. Anything conversational works:

> Plan a 10 day Europe trip from India in April, mid-range budget

> I want a relaxed 5 day trip to Rome and Florence in September for two people

Not sure where to start? Click a suggestion card on the home screen. A plan takes **30–90 seconds**, and you'll see each stage as it progresses.

### Use the trip builder

Prefer fields to sentences? Click the **sliders icon** next to the message box and enter your origin, destination, dates, duration, travellers and budget, plus any interests. Hit **Write my prompt** and your request is composed for you, ready to send or edit.

### Read your plan

Results are split into tabs: **Plan** · **Itinerary** · **Flights** · **Hotels** · **Weather**

### Save, export and revisit

- Every trip is saved to the sidebar automatically; click one to reopen it
- Ask follow-up questions on an open trip and it keeps the context
- Use the icons at the top of a result to **copy**, **download as Markdown** or **print**
- Click **New trip** to start fresh
- Toggle **light / dark mode** with the sun/moon icon at the bottom of the sidebar

## Troubleshooting

<details>
<summary><b>The page loads but planning fails</b></summary>

Check the status indicator at the bottom of the sidebar. If it says *API unreachable*, the app has stopped; restart it with `uv run python app.py`. Otherwise, check the terminal where you started the app for the error.

</details>

<details>
<summary><b>An error says the model does not exist</b></summary>

AI providers retire models over time. List the models your key can use:

```bash
curl -s https://api.groq.com/openai/v1/models \
  -H "Authorization: Bearer $GROQ_API_KEY"
```

Pick one from the list and set it as `GROQ_MODEL` in your `.env`.

</details>

<details>
<summary><b>An error says DATABASE_URL is missing</b></summary>

The app needs PostgreSQL to save your trips. Add a connection string to your `.env`; see [Configuration](#configuration).

</details>

<details>
<summary><b>Plans take a long time</b></summary>

This is expected. Several agents research your trip in turn, and each step involves live data and AI calls. 30–90 seconds is normal.

</details>

## Contributing

Contributions are welcome. The project is under active development, so there's plenty to pick up.

1. **Fork** the repository and clone your fork
2. Follow the [Quick Start](#quick-start) to get it running
3. Create a branch:

   ```bash
   git checkout -b feature/your-feature-name
   ```

4. Make your changes:
   - Keep each pull request focused on one thing
   - Match the style of the surrounding code
   - Check the app still runs end to end
   - Never commit `.env` or API keys
5. Commit with a message that says what changed and why, then push:

   ```bash
   git commit -m "Add support for multi-city trips"
   git push origin feature/your-feature-name
   ```

6. Open a **pull request** against `main` describing what you changed, why, and how you tested it.

**Found a bug or have an idea?** Open an [issue](https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/issues). For bugs, include what you did, what you expected, what happened instead, and any terminal error output.

## Authors

- [@KalyanM45](https://github.com/KalyanM45): creator and maintainer

See also the list of [contributors](https://github.com/KalyanM45/TripPilot-Multi-Agent-AI-Travel-Planner/graphs/contributors).

## Acknowledgements

- [Groq](https://groq.com/) for fast AI inference
- [Tavily](https://tavily.com/), [AviationStack](https://aviationstack.com/) and [OpenWeather](https://openweathermap.org/) for the live travel data

## License

Licensed under the **GNU General Public License v3.0**. See [LICENSE](LICENSE).
