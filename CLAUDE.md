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

## Claude Code subagents

This repo also hosts a small research/reporting pipeline built on Claude Code
subagents, unrelated to the Streamlit app:

- `.claude/agents/market-analyst.md` — researches and ranks potential stocks to buy
  in the current week (uses web search + the Massive market-data MCP tools), saves
  its report under `reports/weekly/`.
- `.claude/agents/email-writer.md` — reads a report and synthesizes it into a
  concise, ready-to-send email; only creates an actual Gmail draft if explicitly
  asked.
- `.claude/commands/weekly-market-email.md` — the `/weekly-market-email` slash
  command that runs both subagents in sequence (market-analyst → email-writer).
- `reports/weekly/` — dated output of each pipeline run (`*-market-analysis.md`,
  `*-email.md`), committed for history.

To run the full pipeline, use `/weekly-market-email`. To invoke either subagent
directly, describe the task and Claude Code will delegate based on the agent
`description` fields, or reference it by name.
