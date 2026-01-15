# Repository Guidelines

## Purpose (For Codex Agents)
This repo is a Python MCP server that exposes a `gemini` tool by shelling out to the external Gemini CLI. Optimize for small, reviewable diffs and production-safe behavior.

## Project Structure
- `src/geminimcp/`: package
  - `server.py`: FastMCP server, subprocess wrapper, JSON event aggregation
  - `cli.py`: console entry (`geminimcp`)
- `docs/`, `images/` for documentation and assets
- `pyproject.toml`, `uv.lock` for packaging and dependency pinning

## Setup & Local Commands
- Install/sync: `uv sync`
- Run server over stdio: `uv run geminimcp`
- Quick module run: `uv run python -m geminimcp.cli`

## Coding Guidelines
- Python 3.12+, 4-space indent, type hints preferred; keep docstrings consistent with existing modules.
- Preserve cross-platform behavior (Windows/WSL/Linux). Always quote paths with double-quotes and prefer forward slashes in docs/examples.
- When touching subprocess code: keep `shell=False`, validate user-provided paths (e.g., `cd`), and avoid command injection.

## Testing & Verification
- No test harness is configured. For behavior changes, include a manual verification section (exact commands + expected output).
- If adding tests, prefer stdlib `unittest` under `tests/test_*.py` and avoid calling the real `gemini` binary.

## Safety, Secrets, and Git
- Do not delete files, run `git commit/push`, or `git reset --hard` without explicit user confirmation.
- Network access may be restricted; do not fetch remote resources during normal runs.
- Never log or commit credentials; rely on the Gemini CLI’s own auth/config.
