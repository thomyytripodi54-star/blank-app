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
