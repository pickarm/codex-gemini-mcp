![Banner](../images/title.png)

<div align="center">

**Gemini MCP Server for Codex**

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](../LICENSE) [![Python Version](https://img.shields.io/badge/python-3.12+-blue.svg)](https://www.python.org/downloads/) [![MCP Compatible](https://img.shields.io/badge/MCP-Compatible-green.svg)](https://modelcontextprotocol.io)

English | [Chinese](../README.md)

</div>

---

## Overview

**Gemini-MCP** is an MCP server that lets Codex invoke the local Gemini CLI as a tool. It runs the CLI, consumes stream-json output, and returns structured MCP responses for session continuation and result consumption.

## Key Features

- Standard MCP tool interface: `gemini`
- stdio transport for Codex MCP clients
- Session continuation via `SESSION_ID`
- Optional `sandbox` mode
- Cross-platform (Windows/WSL/Linux)

## Quick Start

### Prerequisites

- Gemini CLI installed and configured
- uv installed
- Python 3.12+

### Register in Codex

Add a stdio MCP server entry in Codex with the following command (run by Codex, not directly in a terminal):

```bash
uvx --from git+https://github.com/pickarm/codex-gemini-mcp.git geminimcp
```

For a fixed version, replace `git+...` with a tag or commit.

One-shot add to Codex config (PowerShell):

```powershell
codex mcp add-json gemini '{ "command": "uvx", "args": ["--from", "git+https://github.com/pickarm/codex-gemini-mcp.git", "geminimcp"] }'
```

If you already installed the package locally, you can use the local entry:

```powershell
codex mcp add-json gemini '{ "command": "geminimcp", "args": [] }'
```

You can also configure it via a config file (field names may vary by Codex version):

```json
{
  "mcpServers": {
    "gemini": {
      "command": "uvx",
      "args": [
        "--from",
        "git+https://github.com/pickarm/codex-gemini-mcp.git",
        "geminimcp"
      ]
    }
  }
}
```

### Install Only (No Server Start)

If you only want to install the package and avoid terminal JSON-RPC errors (no MCP server start), run:

```bash
uv pip install "git+https://github.com/pickarm/codex-gemini-mcp.git"
```

### Local Run (Optional)

```bash
git clone https://github.com/pickarm/codex-gemini-mcp.git
cd "codex-gemini-mcp"
uv sync
uv run geminimcp
```

Or:

```bash
uv run python -m geminimcp.cli
```

### Windows Installation & Setup

No dedicated Windows build is required; the flow is the same:

1. Install and configure Gemini CLI, then confirm `gemini` is on PATH:
   - `where gemini`
2. Install uv and make sure `uv`/`uvx` are on PATH:
   - `uv --version`
3. Register the MCP server using the `uvx` command above; prefer absolute `cd` paths with forward slashes.

## Tool Documentation

### gemini Parameters

| Parameter | Type | Required | Default | Description |
|------|------|------|--------|------|
| `PROMPT` | `str` | Yes | - | Task instructions sent to Gemini |
| `cd` | `path` | Yes | - | Working directory / workspace root for Gemini CLI |
| `sandbox` | `bool` | No | `False` | Enable sandbox mode |
| `SESSION_ID` | `str` | No | `""` | Session ID (empty for new session) |
| `return_all_messages` | `bool` | No | `False` | Return complete message history |
| `model` | `str` | No | `""` | Specify model (defaults to Gemini CLI config) |

Use an absolute path for `cd` and prefer forward slashes, for example:

```json
{
  "PROMPT": "Explain MCP in one sentence.",
  "cd": "D:/ai/workspace"
}
```

Resume example:

```json
{
  "PROMPT": "Continue the previous topic.",
  "cd": "D:/ai/workspace",
  "SESSION_ID": "session-uuid"
}
```

### Return Structure

**On success:**
```json
{
  "success": true,
  "SESSION_ID": "session-uuid",
  "agent_messages": "Gemini's response content..."
}
```

**When return_all_messages is enabled:**
```json
{
  "all_messages": [...]
}
```

**On failure:**
```json
{
  "success": false,
  "error": "Error description"
}
```

## Security & Privacy

- This project only invokes the local Gemini CLI and does not store credentials.
- Authentication and network behavior are handled by Gemini CLI configuration.

## Development & Contributing

```bash
git clone https://github.com/pickarm/codex-gemini-mcp.git
cd "codex-gemini-mcp"
uv sync
```

Local run:
```bash
uv run geminimcp
```

## FAQ

<details>
<summary>Q1: The `gemini` command is not found.</summary>

Make sure Gemini CLI is installed and authenticated per its official docs, and that `gemini` is available on PATH. On Windows, restart your terminal and try again.

</details>

<details>
<summary>Q2: Why is `SESSION_ID` missing?</summary>

This tool depends on Gemini CLI `stream-json` output. If the output is intercepted or malformed, session parsing can fail.

</details>

## License

This project is licensed under the [MIT License](../LICENSE).
