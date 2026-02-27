# Specification: Claude Code Telemetry Dashboard

## Objective
Build a real-time, terminal-based dashboard using `ink` that tracks Claude Code token usage and estimated costs over specific time windows (1 Hour, 4 Hours, 24 Hours). This must be an elite, highly optimized, open-source-ready CLI tool.

## Architecture & Efficiency (No Polling)
Do **not** use `setInterval` polling to read the disk. 
Instead, use the `chokidar` library to create a lightweight, event-driven filesystem watcher on the target directory. The UI should only parse files and re-render when a file `add` or `change` event is detected by the OS.

## Data Source
Claude Code stores its session data as JSON files in `~/.claude/todos/`. 
1. The app needs to watch and read the JSON files in this directory.
2. It needs to extract the timestamps, the model used, and the token usage.
3. *Note to Agent: Before writing the main app, you must autonomously read a recent JSON file in that directory to understand its exact schema so you can type it correctly. Extract the actual JSON keys Anthropic uses for token counting.*

## Pricing Math (per 1 Million Tokens - 2026 Rates)
- `claude-opus-4-6`: $5.00 Input / $25.00 Output
- `claude-sonnet-4-6`: $3.00 Input / $15.00 Output
- `claude-haiku-4-5`: $1.00 Input / $5.00 Output
*(Implement a fallback to Sonnet 4.6 pricing if an unknown model string is encountered).*

## UI Requirements
Use the `ink` library to build a responsive, hacker-aesthetic terminal UI.
1. **Header:** Display a title ("🤖 CLAUDE TELEMETRY") and the currently active Model.
2. **Grid:** Display three distinct columns (Last 1h, Last 4h, Last 24h).
3. **Data Points:** For each column, display:
   - Total Input Tokens (formatted nicely, e.g., "12.5k")
   - Total Output Tokens
   - Total Estimated Cost (formatted precisely as $X.XXXX)