# Copilot Instructions for Agentic Drop Zone

## Project Overview

Agentic Drop Zone is an automated file processing system that monitors directories and triggers AI agents (Claude Code, Gemini CLI, Codex CLI) when files are dropped. It is highly configurable via `drops.yaml` and supports agent-agnostic workflows, parallel agent execution, and rich streaming output.

## Key Files & Directories
- `sfs_agentic_drop_zone.py`: Main entry point, orchestrates file watching and agent invocation.
- `drops.yaml`: Configures drop zones, file patterns, agents, prompts, and event triggers.
- `example_input_files/`: Sample files for testing drop zone workflows.
- `edit_image_input_files/`, `images/`, `specs/`: Supporting assets and documentation.
- `ai_docs/`: AI agent documentation and integration notes.

## Architecture & Data Flow
- Uses Watchdog to monitor directories for file events.
- Matches dropped files against patterns in `drops.yaml`.
- Loads prompt templates and replaces variables (e.g., `FILE_PATH`).
- Selects and invokes the configured agent (Claude Code, Gemini CLI, Codex CLI).
- Streams agent responses to the console in styled panels.
- Archives processed files and manages output directories.

## Developer Workflows
- **Run the system:**
  - Install dependencies: `curl -LsSf https://astral.sh/uv/install.sh | sh`
  - Set environment variables (e.g., `ANTHROPIC_API_KEY`, `CLAUDE_CODE_PATH`).
  - Start: `uv run sfs_agentic_drop_zone.py`
- **Test workflows:**
  - Copy files from `example_input_files/` into configured drop zone directories.
- **Configure agents:**
  - Edit `drops.yaml` to set agent type, model, prompt, and MCP server config.
  - For Claude Code/Gemini CLI, copy and edit `.mcp.json.sample`/`.gemini/settings.json.sample` for API keys.

## Project-Specific Patterns & Conventions
- Drop zone configuration is centralized in `drops.yaml`.
- Prompts are loaded from markdown files and support variable substitution.
- Claude Code runs with `bypassPermissions` for full tool access; Gemini CLI uses `--yolo --sandbox` flags.
- Output directories and archiving are managed automatically per workflow.
- Bash commands are used for efficient file operations (e.g., appending data).
- Audio transcription uses OpenAI Whisper (local install via `uv tool install openai-whisper`).

## Integration Points
- Claude Code and Gemini CLI support MCP server integration for tool access.
- Replicate AI models are used for image generation/editing (requires `REPLICATE_API_TOKEN`).
- Whisper is used for audio transcription (no API key required).

## Examples
- See `drops.yaml` for agent and workflow configuration.
- See `README.md` for architecture diagrams and workflow details.

## Warnings
- Agents may execute dangerous actions; permissions are intentionally broad for agentic workflows.
- Codex CLI is not yet implemented.

---

**Feedback Requested:**
If any section is unclear or missing important project-specific details, please specify so it can be improved for future AI coding agents.
