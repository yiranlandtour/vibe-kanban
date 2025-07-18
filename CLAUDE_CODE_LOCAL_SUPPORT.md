# Claude Code Local Support

This document describes the enhanced Claude Code integration in Vibe Kanban that supports local Claude Code installations.

## Features

### 1. Automatic Local Claude Code Detection
The system automatically detects if `claude-code` is installed locally by:
- Checking if `claude-code` is available in PATH using `which` or `where` commands
- Looking in common installation directories:
  - `/usr/local/bin/claude-code`
  - `/usr/bin/claude-code`
  - `/opt/homebrew/bin/claude-code`
  - `~/.local/bin/claude-code`

### 2. Configuration File Support
You can specify a custom Claude Code path in your `.claude.json` configuration file:

```json
{
  "claudeCodePath": "/path/to/your/claude-code",
  "mcpServers": {
    // your MCP server configurations
  }
}
```

### 3. Command Priority
The system uses the following priority order for selecting the Claude Code command:
1. **Configuration file path** - If `claudeCodePath` is specified in `.claude.json`
2. **Local installation** - If `claude-code` is detected in the system
3. **NPX fallback** - Falls back to `npx -y @anthropic-ai/claude-code@latest`

### 4. Automatic Fallback on Failure
If the primary command fails to execute (e.g., local installation is corrupted), the system automatically falls back to the npx version with appropriate logging:
- Primary command failure is logged as a warning
- Fallback attempt is made transparently
- If fallback also fails, a detailed error is provided

## Benefits

1. **Performance**: Using a local Claude Code installation is significantly faster than downloading via npx each time
2. **Offline Support**: Works without internet connection if Claude Code is installed locally
3. **Version Control**: You can pin a specific version of Claude Code locally
4. **Flexibility**: Supports custom installations and configurations

## Implementation Details

The implementation includes:
- Static caching of local Claude Code detection results for performance
- Async detection functions that run once per session
- Robust error handling with automatic fallback
- Support for both normal and plan modes
- Cross-platform compatibility (Windows, macOS, Linux)

## Testing

Run the tests with:
```bash
cargo test --package vibe-kanban --lib -- executors::claude::tests
```

The test suite includes:
- Command building tests for different modes
- Fallback behavior verification
- Path detection tests
- Script generation tests