# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

This is the Streamlit "blank app" template — a minimal starting point for a Streamlit application, not yet built out into a real project. The entire app currently lives in `streamlit_app.py`.

## Commands

Install dependencies:
```
pip install -r requirements.txt
```

Run the app locally:
```
streamlit run streamlit_app.py
```

There are no tests, linter, or build step configured in this repo yet.

## Architecture

- `streamlit_app.py` — the single entry point Streamlit runs. Add new pages/components by extending this file (or, once the app grows, by introducing a `pages/` directory per Streamlit's multipage convention).
- `requirements.txt` — Python dependencies; currently just `streamlit`. Add new packages here as they're introduced.
- `.devcontainer/devcontainer.json` — GitHub Codespaces / VS Code Dev Containers config. On container start it installs `requirements.txt` (and `packages.txt` if present) and runs `streamlit run streamlit_app.py --server.enableCORS false --server.enableXsrfProtection false`, forwarding port 8501.

## Weekly stock report system (Claude Code automation)

This repo also hosts a Claude Code automation, unrelated to the Streamlit app above, that produces a weekly stock/crypto research report by email. It runs entirely inside Claude Code (subagents + a skill), not as Python code in this repo.

- `.claude/agents/market-analyst.md` — subagent that researches the watchlist using the Massive MCP connector (prices, fundamentals, technicals) plus web search for qualitative context, and picks the 10 best opportunities of the week with an extended per-asset writeup and a buy/hold/sell call.
- `.claude/agents/report-designer.md` — subagent that takes the analyst's raw research and turns it into a polished HTML report with an executive summary, a recommendations table, and key actionables, then creates it as a Gmail draft (via the Gmail MCP connector) and archives a copy under `reports/`.
- `.claude/skills/weekly-stock-report/SKILL.md` — orchestrates the two subagents in sequence. Invoke with the `weekly-stock-report` skill, or by asking for "el reporte semanal de acciones".
- `.claude/skills/weekly-stock-report/watchlist.json` — the fixed universe of tickers to evaluate each week, how many to select, and the recipient email. Edit this file to change the watchlist.
- `reports/` — local (gitignored) archive of each week's generated HTML report.

Scheduling is done via a Claude Code Routine (`create_trigger`) that fires a prompt like "Ejecutá el skill weekly-stock-report" on a weekly cron — it is not configured as a GitHub Actions workflow. The Gmail MCP connector currently only supports creating drafts, not sending — the skill always leaves a draft for manual review/send, never an auto-sent email.
