# NBA Stats AI Analyst

A Jupyter notebook that fetches live NBA season stats, stores them in a local SQLite database, and uses the open-source **Gemma4** model (via Ollama) to answer natural-language basketball questions by inventing custom stats, writing SQL, and generating charts.

## What it does

1. **Fetches data** — Pulls 2025-26 player and team stats from NBA.com using the `nba_api` library.
2. **Stores to SQLite** — Saves player and team tables to `nba_stats.db` and creates a relational view joining the two.
3. **AI-powered analysis** — Ask any basketball question in plain English (e.g. *"Who are the most complete two-way players?"*) and Gemma4 will:
   - Invent a custom statistic (like PER or QBR) using the available columns
   - Write a SQL query to compute it
   - Generate and execute a matplotlib bar chart of the results

## Educational goals

- Working with **SQLite databases**: creating tables, writing SQL queries, and building relational views from real-world data
- Using **open-source AI models**: running Gemma4 locally via Ollama for natural-language-to-SQL and code generation tasks

## Requirements

- [Ollama](https://ollama.com) with the `gemma4` model pulled (`ollama pull gemma4`)
- Python packages: `nba_api`, `pandas`, `ollama`, `matplotlib`

```bash
pip install nba_api pandas ollama matplotlib
```

## Usage

Run the notebook cells top to bottom. The final cell lets you type any question:

```python
analyze("Who are the most efficient scorers?")
analyze("Which players contribute most on defense?")
```
