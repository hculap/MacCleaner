# MacCleaner Development Guide

## Plugin Structure

```
MacCleaner/
├── .claude-plugin/
│   └── plugin.json          # Plugin manifest
├── commands/                 # Slash commands
│   ├── disk-audit.md
│   ├── disk-clean.md
│   ├── memory-audit.md
│   ├── memory-clean.md
│   └── security-scan.md
├── agents/                   # Specialized subagents
│   ├── disk-analyzer.md
│   ├── ram-analyzer.md
│   └── security-analyzer.md
├── skills/                   # Knowledge bases
│   ├── disk-cleanup-patterns/
│   ├── memory-management/
│   ├── security-scanning/
│   ├── docker-cleanup/
│   ├── developer-cleanup/
│   └── homebrew-maintenance/
├── .mcp.json                 # MCP server config (VirusTotal)
└── scripts/                  # Helper scripts
```

## Testing the Plugin

```bash
# Run Claude Code with this plugin
claude --plugin-dir /Users/szymonpaluch/Projects/MacCleaner
```

## Commands

| Command | Description |
|---------|-------------|
| `/mac-cleaner:disk-audit` | Full disk usage analysis |
| `/mac-cleaner:disk-clean` | Interactive disk cleanup |
| `/mac-cleaner:memory-audit` | RAM usage analysis |
| `/mac-cleaner:memory-clean` | Free up memory |
| `/mac-cleaner:security-scan` | Security threat scan |

## Agents

| Agent | Triggers On | Color |
|-------|-------------|-------|
| disk-analyzer | Disk space, storage issues | Blue |
| ram-analyzer | Slow Mac, memory issues | Green |
| security-analyzer | Malware, security concerns | Red |

## Skills

Skills are activated automatically based on user queries:

- **Disk Cleanup Patterns** - Safe file deletion guidance
- **Memory Management** - macOS memory concepts
- **Security Scanning** - Malware detection patterns
- **Docker Cleanup** - Docker resource management
- **Developer Cleanup** - Build artifacts, node_modules
- **Homebrew Maintenance** - Package management

## MCP Integration

VirusTotal MCP is optional for deep security scans:

1. Get API key from https://www.virustotal.com/
2. Set environment variable: `export VIRUSTOTAL_API_KEY=your_key`
3. Use `--deep` flag with security-scan

## Development Notes

### Requirements

- macOS 12+ (Monterey or later)
- Homebrew (optional, for brew commands)
- Docker Desktop (optional, for Docker cleanup)

### Safety Guidelines

1. **Never auto-delete** user files - always confirm
2. **Prefer Trash** over permanent deletion
3. **Explain implications** before destructive actions
4. **Respect excluded paths** in user settings

### Adding New Features

**New Command**:
1. Create `.md` file in `commands/`
2. Add YAML frontmatter with `description`, `allowed-tools`, `argument-hint`
3. Use `## Step N:` format for steps
4. Reference `$ARGUMENTS` for user input

**New Agent**:
1. Create `.md` file in `agents/`
2. Add `description` with `<example>` blocks and `<commentary>`
3. Set `model`, `color`, `tools`

**New Skill**:
1. Create subdirectory in `skills/`
2. Add `SKILL.md` with frontmatter
3. Include `allowed-tools` field
4. Add reference files in `references/` subdirectory

## User Settings

Users can create `.claude/mac-cleaner.local.md` with:

```yaml
---
large_file_threshold_mb: 100
old_file_days: 180
auto_confirm_cache_cleanup: false
excluded_paths:
  - ~/Documents/Important
  - ~/Projects/active-project
virustotal_enabled: false
---
```
