# Agentic Drop Zone - Agent Guidelines

## Build/Lint/Test Commands

### Running the Application
```bash
# Install uv (if not already installed)
curl -LsSf https://astral.sh/uv/install.sh | sh

# Run the main application
uv run sfs_agentic_drop_zone.py

# Run with specific Python version
uv run --python 3.11 sfs_agentic_drop_zone.py
```

### Testing
- No formal test suite exists
- Test workflows by copying files from `example_input_files/` to configured drop zone directories
- Example: `cp example_input_files/echo.txt agentic_drop_zone/echo_zone/`

### Linting/Type Checking
- No linting tools configured
- Use Python's built-in type checking: `python -m mypy sfs_agentic_drop_zone.py` (if mypy installed)
- Code follows PEP 8 style guidelines

## Code Style Guidelines

### Python Conventions
- **Type Hints**: Use comprehensive type annotations (`Optional[str]`, `list[str]`, `dict[str, Any]`)
- **Imports**: Group imports (stdlib, third-party, local) with blank lines between groups
- **String Formatting**: Use f-strings for all string interpolation
- **Error Handling**: Use try/except blocks with specific exception types
- **Async/Await**: Use asyncio for all I/O operations and subprocess calls
- **Path Handling**: Use `pathlib.Path` instead of string paths
- **Constants**: Use UPPER_CASE for constants, define at module level
- **Enums**: Use `enum.StrEnum` or `enum.Enum` for string constants and options

### Naming Conventions
- **Classes**: PascalCase (e.g., `DropZone`, `AgenticDropZone`)
- **Methods/Functions**: snake_case (e.g., `build_prompt`, `process_file`)
- **Variables**: snake_case (e.g., `file_path`, `zone_config`)
- **Constants**: UPPER_CASE (e.g., `FILE_PATH_PLACEHOLDER`)
- **Private Methods**: Leading underscore (e.g., `_should_process_event`)

### Code Structure Patterns
- **Data Models**: Use Pydantic `BaseModel` with `Field` descriptions and validators
- **Configuration**: Use YAML files for runtime configuration (`drops.yaml`)
- **Logging**: Use Python's `logging` module with appropriate levels
- **Console Output**: Use Rich library for styled console output with panels and colors
- **Environment Variables**: Check required env vars at startup with helpful error messages

### Documentation
- **Docstrings**: Use Google-style docstrings for all public methods
- **Comments**: Add comments for complex logic, avoid obvious comments
- **Type Comments**: Not needed with full type hints

### File Organization
- **Single File Script**: Main logic in `sfs_agentic_drop_zone.py` (PEP 723 compliant)
- **Configuration**: `drops.yaml` for drop zone definitions
- **Prompts**: Markdown files in `.claude/commands/` for reusable prompts
- **Environment**: `.env` file for sensitive configuration

### Dependencies
- **Inline Metadata**: Dependencies declared in script header using PEP 723 format
- **Runtime Dependencies**: `claude-code-sdk`, `pydantic`, `watchdog`, `pyyaml`, `python-dotenv`, `rich`
- **Optional Dependencies**: `openai-whisper` for audio transcription

## Copilot Instructions

### Project Overview
Agentic Drop Zone is an automated file processing system that monitors directories and triggers AI agents (Claude Code, Gemini CLI, Codex CLI) when files are dropped. It is highly configurable via `drops.yaml` and supports agent-agnostic workflows, parallel agent execution, and rich streaming output.

### Key Files & Directories
- `sfs_agentic_drop_zone.py`: Main entry point, orchestrates file watching and agent invocation.
- `drops.yaml`: Configures drop zones, file patterns, agents, prompts, and event triggers.
- `example_input_files/`: Sample files for testing drop zone workflows.
- `edit_image_input_files/`, `images/`, `specs/`: Supporting assets and documentation.
- `ai_docs/`: AI agent documentation and integration notes.

### Architecture & Data Flow
- Uses Watchdog to monitor directories for file events.
- Matches dropped files against patterns in `drops.yaml`.
- Loads prompt templates and replaces variables (e.g., `FILE_PATH`).
- Selects and invokes the configured agent (Claude Code, Gemini CLI, Codex CLI).
- Streams agent responses to the console in styled panels.
- Archives processed files and manages output directories.

### Developer Workflows
- **Run the system:** `uv run sfs_agentic_drop_zone.py`
- **Test workflows:** Copy files from `example_input_files/` into configured drop zone directories.
- **Configure agents:** Edit `drops.yaml` to set agent type, model, prompt, and MCP server config.

### Project-Specific Patterns & Conventions
- Drop zone configuration is centralized in `drops.yaml`.
- Prompts are loaded from markdown files and support variable substitution.
- Claude Code runs with `bypassPermissions` for full tool access; Gemini CLI uses `--yolo --sandbox` flags.
- Output directories and archiving are managed automatically per workflow.
- Bash commands are used for efficient file operations (e.g., appending data).
- Audio transcription uses OpenAI Whisper (local install via `uv tool install openai-whisper`).

### Integration Points
- Claude Code and Gemini CLI support MCP server integration for tool access.
- Replicate AI models are used for image generation/editing (requires `REPLICATE_API_TOKEN`).
- Whisper is used for audio transcription (no API key required).

### Warnings
- Agents may execute dangerous actions; permissions are intentionally broad for agentic workflows.
- Codex CLI is not yet implemented.

## Security Considerations
- **Environment Variables**: Never commit API keys or sensitive data
- **File Permissions**: Agents run with broad permissions for tool access
- **Sandboxing**: Gemini CLI uses `--sandbox` flag to limit file system access
- **Error Handling**: Always include comprehensive error handling for file operations</content>
<parameter name="filePath">/home/joel/source/agentic-drop-zones/AGENTS.md