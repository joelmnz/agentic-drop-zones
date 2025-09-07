# OpenCode Documentation

## Overview

OpenCode is an AI coding agent built for the terminal, similar to Claude Code but with key differences:

- 100% open source
- Provider-agnostic (works with Anthropic, OpenAI, Google, local models)
- Focus on Terminal User Interface (TUI)
- Client/server architecture allowing remote access

## Installation

### One-liner Install

```bash
curl -fsSL https://opencode.ai/install | bash
```

### Package Managers

```bash
# npm/bun/pnpm/yarn
npm i -g opencode-ai@latest

# Homebrew (macOS and Linux)
brew install sst/tap/opencode

# Arch Linux
paru -S opencode-bin
```

**Note**: Remove versions older than 0.1.x before installing.

### Installation Directory Priority

1. `$OPENCODE_INSTALL_DIR` - Custom installation directory
2. `$XDG_BIN_DIR` - XDG Base Directory Specification compliant path
3. `$HOME/bin` - Standard user binary directory (if exists or can be created)
4. `$HOME/.opencode/bin` - Default fallback

## Usage

OpenCode is a terminal-based AI coding agent that can be used in both interactive TUI mode and non-interactive CLI mode.

### Interactive Mode (TUI)

By default, running `opencode` without arguments starts the Terminal User Interface:

```bash
opencode
```

### Non-Interactive CLI Mode

For automation and scripting, use the `run` command to execute prompts directly:

```bash
opencode run "Explain how closures work in JavaScript"
```

#### Run Command Flags

- `--continue` / `-c`: Continue the last session
- `--session` / `-s`: Session ID to continue
- `--share`: Share the session
- `--model` / `-m`: Model to use in the format `provider/model`
- `--agent`: Agent to use

Example usage:

```bash
# Use specific model
opencode run --model anthropic/claude-sonnet-4-20250514 "Generate a React component"

# Continue previous session
opencode run --continue "Add error handling to the component"

# Use specific agent
opencode run --agent code-reviewer "Review this code for issues"
```

### Authentication

Before using OpenCode, configure API keys for your preferred providers:

```bash
# Login to configure API keys
opencode auth login

# List configured providers
opencode auth list

# Logout from a provider
opencode auth logout
```

Credentials are stored in `~/.local/share/opencode/auth.json`.

### Model Management

List all available models from configured providers:

```bash
opencode models
```

Models are displayed in the format `provider/model`.

### Server Mode

Start OpenCode as a headless server for API access:

```bash
opencode serve
```

#### Server Flags

- `--port` / `-p`: Port to listen on
- `--hostname` / `-h`: Hostname to listen on

### Global CLI Flags

OpenCode supports these global flags across all commands:

- `--help` / `-h`: Display help
- `--version`: Print version number
- `--print-logs`: Print logs to stderr
- `--log-level`: Log level (DEBUG, INFO, WARN, ERROR)
- `--prompt` / `-p`: Prompt to use
- `--model` / `-m`: Model to use in the format `provider/model`
- `--agent`: Agent to use
- `--port`: Port to listen on
- `--hostname`: Hostname to listen on

### Agent Management

Manage custom agents:

```bash
# Create a new agent
opencode agent create

# List agents
opencode agent [command]
```

### GitHub Integration

Manage GitHub automation:

```bash
# Install GitHub agent in repository
opencode github install

# Run GitHub agent (typically in CI/CD)
opencode github run --event push --token $GITHUB_TOKEN
```

### Upgrade

Update OpenCode to the latest version:

```bash
# Upgrade to latest
opencode upgrade

# Upgrade to specific version
opencode upgrade v0.1.48

# Specify installation method
opencode upgrade --method npm
```

## Configuration

OpenCode uses JSON or JSONC (JSON with Comments) configuration files. The configuration supports a comprehensive schema for customizing behavior, providers, models, and more.

### Configuration File Format

OpenCode supports both JSON and JSONC formats:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  // Theme configuration
  "theme": "opencode",
  "model": "anthropic/claude-sonnet-4-20250514",
  "autoupdate": true
}
```

### Configuration File Locations

OpenCode looks for configuration files in the following order of precedence:

#### Global Configuration

Place global config at `~/.config/opencode/opencode.json` for system-wide settings like themes, providers, or keybinds.

#### Per-Project Configuration

Add `opencode.json` in your project root. This takes precedence over global config and is useful for project-specific settings. The file is safe to check into Git.

OpenCode searches for project config in the current directory and traverses up to the nearest Git directory.

#### Custom Path

Specify a custom config file using the `OPENCODE_CONFIG` environment variable:

```bash
export OPENCODE_CONFIG=/path/to/my/custom-config.json
opencode run "Hello world"
```

### Configuration Schema

#### Models and Providers

Configure providers and models through the `provider`, `model`, and `small_model` options:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "provider": {},
  "model": "anthropic/claude-sonnet-4-20250514",
  "small_model": "anthropic/claude-3-5-haiku-20241022"
}
```

The `small_model` is used for lightweight tasks like title generation. OpenCode automatically uses cheaper models when available.

#### Themes

Configure the UI theme:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "theme": "opencode"
}
```

#### Agents

Define specialized agents for specific tasks:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "agent": {
    "code-reviewer": {
      "description": "Reviews code for best practices and potential issues",
      "model": "anthropic/claude-sonnet-4-20250514",
      "prompt": "You are a code reviewer. Focus on security, performance, and maintainability.",
      "tools": {
        // Disable file modification tools for review-only agent
        "write": false,
        "edit": false
      }
    }
  }
}
```

Agents can also be defined using markdown files in `~/.config/opencode/agent/` or `.opencode/agent/`.

#### Sharing

Configure the share feature:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "share": "manual"
}
```

Options:

- `"manual"` - Allow manual sharing via commands (default)
- `"auto"` - Automatically share new conversations
- `"disabled"` - Disable sharing entirely

#### Commands

Define custom commands for repetitive tasks:

```jsonc
{
  "$schema": "https://opencode.ai/config.json",
  "command": {
    "test": {
      "template": "Run the full test suite with coverage report and show any failures.\nFocus on the failing tests and suggest fixes.",
      "description": "Run tests with coverage",
      "agent": "build",
      "model": "anthropic/claude-3-5-sonnet-20241022"
    },
    "component": {
      "template": "Create a new React component named $ARGUMENTS with TypeScript support.\nInclude proper typing and basic structure.",
      "description": "Create a new component"
    }
  }
}
```

Commands can also be defined using markdown files in `~/.config/opencode/command/` or `.opencode/command/`.

#### Keybinds

Customize keybinds:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "keybinds": {}
}
```

#### Autoupdate

Control automatic updates:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "autoupdate": false
}
```

#### Formatters

Configure code formatters:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "formatter": {
    "prettier": {
      "disabled": true
    },
    "custom-prettier": {
      "command": ["npx", "prettier", "--write", "$FILE"],
      "environment": {
        "NODE_ENV": "development"
      },
      "extensions": [".js", ".ts", ".jsx", ".tsx"]
    }
  }
}
```

#### Permissions

Control what AI agents can do:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "edit": "ask",
    "bash": "ask"
  }
}
```

Options for permissions:

- `edit` - Controls file editing operations (`"ask"` or `"allow"`)
- `bash` - Controls bash commands (`"ask"`, `"allow"`, or pattern map)

#### MCP Servers

Configure MCP servers:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "mcp": {}
}
```

#### Instructions

Configure model instructions using file paths:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": [
    "CONTRIBUTING.md",
    "docs/guidelines.md",
    ".cursor/rules/*.md"
  ]
}
```

#### Disabled Providers

Disable specific providers:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "disabled_providers": ["openai", "gemini"]
}
```

### Variable Substitution

OpenCode supports variable substitution in configuration files:

#### Environment Variables

Use `{env:VARIABLE_NAME}` to substitute environment variables:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "{env:OPENCODE_MODEL}",
  "provider": {
    "anthropic": {
      "options": {
        "apiKey": "{env:ANTHROPIC_API_KEY}"
      }
    }
  }
}
```

#### File Contents

Use `{file:path/to/file}` to substitute file contents:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "instructions": ["./custom-instructions.md"],
  "provider": {
    "openai": {
      "options": {
        "apiKey": "{file:~/.secrets/openai-key}"
      }
    }
  }
}
```

File paths can be relative to the config file directory or absolute paths starting with `/` or `~`.

For complete configuration details, refer to the official documentation at <https://opencode.ai/docs/config/>.

## Development

To contribute or run locally:

- Requires Bun and Golang 1.24.x
- Run: `bun install && bun dev`

### Contributing Guidelines

- Bug fixes
- Improvements to LLM performance
- Support for new providers
- Fixes for environment-specific quirks
- Missing standard behavior
- Documentation

**Note**: Core features require design process with core team. No PRs accepted for fundamental features.

## Differences from Claude Code

- 100% open source vs. proprietary
- Provider-agnostic vs. Anthropic-only
- Enhanced TUI focus
- Client/server architecture for remote access
- Can work with local models

## Community

- **Discord**: <https://opencode.ai/discord>
- **YouTube**: <https://www.youtube.com/c/sst-dev>
- **X (Twitter)**: <https://x.com/anomaly_inv>
- **npm**: <https://www.npmjs.com/package/opencode-ai>

## Technical Details

- **License**: MIT
- **Languages**: Go (46.4%), TypeScript (42.2%), MDX (6.5%), CSS (2.9%)
- **Stars**: 22.2k
- **Contributors**: 167+

## Repository

- **GitHub**: <https://github.com/sst/opencode>
- **Latest Release**: v0.6.4 (4 days ago)
- **Issues**: 699 open
- **Pull Requests**: 182 open

## Notes for Integration

OpenCode is well-suited for CLI integration into the Agentic Drop Zone system. Here are the key integration points:

### CLI Command Structure

The primary command for non-interactive execution is:

```bash
opencode run [flags] "prompt text"
```

### Essential Flags for Integration

- `--model` / `-m`: Specify model in format `provider/model` (e.g., `anthropic/claude-sonnet-4-20250514`)
- `--agent`: Use specific agent configuration
- `--continue` / `-c`: Continue from previous session
- `--session` / `-s`: Specify session ID
- `--share`: Enable session sharing

### Integration Environment Variables

- `OPENCODE_CONFIG`: Custom configuration file path
- Provider API keys (e.g., `ANTHROPIC_API_KEY`, `OPENAI_API_KEY`)

### Authentication Setup

Before integration, ensure authentication is configured:

```bash
opencode auth login
```

This stores credentials in `~/.local/share/opencode/auth.json`.

### Configuration for Agentic Drop Zone

For optimal integration, use a project-specific `opencode.json`:

```json
{
  "$schema": "https://opencode.ai/config.json",
  "model": "anthropic/claude-sonnet-4-20250514",
  "permission": {
    "edit": "allow",
    "bash": "allow"
  },
  "autoupdate": false
}
```

### Command Line Examples

```bash
# Basic prompt execution
opencode run "Analyze this code for potential issues"

# With specific model
opencode run --model anthropic/claude-sonnet-4-20250514 "Generate unit tests"

# With custom agent
opencode run --agent code-reviewer "Review the following code"

# Continue session
opencode run --continue "Add error handling"
```

### Output Handling

OpenCode outputs results to stdout/stderr, making it suitable for:

- Real-time streaming in the Agentic Drop Zone interface
- Capturing output for further processing
- Integration with Rich panels for styled display

### Error Handling

Monitor exit codes and stderr for error detection:

- Exit code 0: Success
- Non-zero exit codes: Various error conditions
- Check stderr for detailed error messages

### Server Mode Alternative

For high-throughput scenarios, consider using server mode:

```bash
opencode serve --port 3000
```

This provides HTTP API access for programmatic interaction.

For complete CLI documentation, refer to <https://opencode.ai/docs/cli/>.
