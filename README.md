# NBA Stats AI Analyst

A Jupyter notebook that fetches live NBA season stats, stores them in a local SQLite database, and uses **Claude** (via the Anthropic API) to answer natural-language basketball questions by inventing custom stats, writing SQL, and generating charts.

An Ollama/Gemma4 backend is also supported as a local fallback.

## What it does

1. **Fetches data** — Pulls 2025-26 player stats, team stats, game logs, and player bio data from NBA.com using the `nba_api` library.
2. **Stores to SQLite** — Saves everything to `nba_stats.db` with three tables (`player_stats`, `team_stats`, `game_logs`) and two pre-built views (`players_with_team`, `games_enriched`).
3. **AI-powered analysis** — Ask any basketball question in plain English and Claude will:
   - Invent a custom statistic using real basketball reasoning and the available columns
   - Write a SQL query to compute it
   - Generate and execute a matplotlib chart of the results
   - Auto-save charts to the `charts/` directory
4. **Desktop GUI** — A Tkinter app lets you type questions and browse answers without touching the notebook. Session logs are saved to `sessions/`.

## AI backend

The notebook has a toggle at the top:

```python
USE_CLAUDE = True   # Anthropic Claude API (default)
USE_CLAUDE = False  # Local Ollama model (fallback)
```

**Claude** (default) uses `claude-haiku-4-5` via the Anthropic API with:
- A full agentic tool-calling loop — Claude decides which tools to call and in what order
- Prompt caching on the system prompt + schema to reduce token cost on repeated queries
- Automatic SQL error repair — if a query fails, a second Claude call fixes it and retries
- Dynamic tool creation — Claude can write and register new Python tools that persist to `dynamic_tools.json` and are reloaded in future sessions
- Token usage summary printed after every query (input / output / cache read / cache write)

**Ollama** (fallback) uses a local `gemma4` model with the same tool loop structure, no API key required.

## Educational goals

- Working with **SQLite databases**: creating tables, writing SQL, building relational views, and extending the schema at runtime
- Using the **Anthropic API**: tool use, agentic loops, prompt caching, and multi-turn conversations
- Comparing **cloud vs. local AI**: the same interface works with both Claude and Ollama

## Requirements

### Claude backend (default)
- Anthropic API key set as `ANTHROPIC_API_KEY` in your environment
- `pip install anthropic nba_api pandas matplotlib ephem`

### Ollama backend (fallback)
- [Ollama](https://ollama.com) with `gemma4` pulled (`ollama pull gemma4`)
- `pip install ollama nba_api pandas matplotlib ephem`

## Usage

Run the notebook cells top to bottom to fetch data and set up the database. The final cells launch the GUI:

```python
root = tk.Tk()
NBAStatsApp(root)
root.mainloop()
```

Or call `analyze()` directly from the notebook:

```python
analyze("Who are the most efficient scorers?")
analyze("Which players contribute most on defense?")
analyze("Does player height correlate with rebounding?")
```

## Database schema

| Name | Type | Description |
|------|------|-------------|
| `player_stats` | table | Season averages + bio (height, weight, position, college, draft info) — one row per player |
| `team_stats` | table | Team-level season totals and win/loss record |
| `game_logs` | table | Every individual game played (~26,000 rows, one per player per game) |
| `players_with_team` | view | `player_stats` joined with `team_stats` (adds W, L, TEAM_WIN_PCT) |
| `games_enriched` | view | `game_logs` joined with `player_stats` and `team_stats` |
