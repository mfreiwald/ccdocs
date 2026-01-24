# Claude Code Docs Marketplace (ccdocs)

[![Last Update](https://img.shields.io/github/last-commit/mfreiwald/ccdocs/main.svg?label=docs%20updated)](https://github.com/mfreiwald/ccdocs/commits/main)
[![Platform](https://img.shields.io/badge/platform-macOS%20%7C%20Linux%20%7C%20Windows-blue)]()
[![Marketplace](https://img.shields.io/badge/type-Claude%20Code%20Marketplace-purple)](https://github.com/mfreiwald/ccdocs)

This repository is a Claude Code marketplace that ships the `ccdocs` plugin, which provides instant access to locally mirrored Claude Code documentation from [code.claude.com/docs](https://code.claude.com/docs). Documentation automatically syncs every 3 hours via GitHub Actions.

## Features

- **📚 51 Documentation Topics**: Complete mirror of official Claude Code documentation
- **🔄 Auto-updates**: Documentation syncs automatically every 3 hours
- **⚡ Instant Access**: Read documentation without network latency
- **📝 Changelog Integration**: Access official Claude Code release notes
- **🔍 Change Tracking**: See what documentation has been updated recently

## Installation

### Install from This Marketplace (Recommended)

Inside Claude Code:
```bash
/plugin marketplace add mfreiwald/ccdocs
/plugin install ccdocs@ccdocs-marketplace
```

### Using --plugin-dir Flag (Recommended for Testing)

1. Clone this repository:
   ```bash
   git clone https://github.com/mfreiwald/ccdocs.git
   cd ccdocs
   ```

2. Use the plugin with Claude Code:
   ```bash
   claude --plugin-dir /path/to/ccdocs/plugins/ccdocs
   ```

## Usage

The plugin provides a single skill: `/ccdocs:docs`

Updates are automatic via GitHub Actions; there is no manual refresh command.

### List All Topics

```bash
/ccdocs:docs
```

Shows all 51 available documentation topics organized by category.

### Read Specific Documentation

```bash
/ccdocs:docs hooks          # Read hooks documentation
/ccdocs:docs mcp            # Read MCP server documentation
/ccdocs:docs skills         # Read skills documentation
/ccdocs:docs plugins        # Read plugins documentation
/ccdocs:docs best-practices # Read best practices guide
```

### See Recent Changes

```bash
/ccdocs:docs what's new     # Show recent documentation updates
```

### Read Claude Code Changelog

```bash
/ccdocs:docs changelog      # Read official release notes
```

### Creative Usage

The skill works great with natural language:

```bash
/ccdocs:docs how do I use environment variables?
/ccdocs:docs explain the differences between hooks and MCP
/ccdocs:docs find all mentions of authentication
```

## Available Documentation Topics

The plugin includes 51 documentation topics:

**Core Documentation:**
- overview, quickstart, features-overview, how-claude-code-works
- setup, cli-reference, common-workflows, best-practices
- interactive-mode, headless

**Configuration:**
- settings, terminal-config, network-config, model-config
- output-styles, statusline

**Features:**
- skills, plugins, plugins-reference, discover-plugins, plugin-marketplaces
- hooks, hooks-guide
- mcp
- memory
- checkpointing
- sub-agents
- sandboxing

**Integrations:**
- vs-code, jetbrains, desktop, chrome
- github-actions, gitlab-ci-cd, devcontainer, slack
- claude-code-on-the-web

**Cloud Providers:**
- amazon-bedrock, google-vertex-ai, microsoft-foundry

**Enterprise:**
- third-party-integrations, llm-gateway
- iam, monitoring-usage, analytics
- security, data-usage, legal-and-compliance
- costs

**Reference:**
- troubleshooting
- changelog

## How It Works

1. **Local Mirror**: All documentation files are stored in `plugins/ccdocs/skills/docs/references/`
2. **Metadata Tracking**: `plugins/ccdocs/skills/docs/docs_manifest.json` tracks file hashes and update times
3. **Auto-sync**: GitHub Actions runs every 3 hours to fetch latest documentation
4. **Git-based Change Tracking**: The plugin uses git history to show recent documentation updates
5. **Skill-based Access**: Claude uses the `/ccdocs:docs` skill to read and present documentation

## Architecture

```
ccdocs/
├── .claude-plugin/
│   └── marketplace.json         # Marketplace manifest
├── .github/                     # Repository automation
│   └── workflows/
│       └── update-docs.yml      # Auto-update workflow
├── scripts/                     # Repository maintenance
│   └── fetch_claude_docs.py     # Documentation fetcher (uv-managed deps)
└── plugins/
    └── ccdocs/                  # The plugin
        ├── .claude-plugin/
        │   └── plugin.json      # Plugin manifest
        └── skills/
            └── docs/            # The skill
                ├── SKILL.md     # Skill definition
                ├── docs_manifest.json # Metadata and sync info
                └── references/  # Documentation (supporting files)
                    └── *.md     # 51 documentation files
```

## Comparison with Standalone Version

This marketplace replaces the earlier standalone claude-code-docs tool.

| Feature | Standalone | Plugin |
|---------|------------|--------|
| Documentation Access | ✅ `/docs` command | ✅ `/ccdocs:docs` skill |
| Auto-updates | ✅ Git hooks | ✅ GitHub Actions + git pull |
| Installation | Complex bash scripts | Simple git clone |
| Namespace | Global `/docs` | Namespaced `/ccdocs:docs` |
| Configuration | hooks + settings.json | Just plugin directory |
| Dependencies | jq, curl, git | Just git |
| Maintenance | Manual updates | Automatic via GitHub Actions |
| Integration | System-wide | Per-session or global |

**Key Advantages of the Plugin:**

1. **Simpler Installation**: No bash scripts, settings.json modifications, or command file creation
2. **Better Namespacing**: `/ccdocs:docs` won't conflict with user-created commands
3. **Cleaner Architecture**: Uses Claude's native plugin system
4. **Easier Maintenance**: Standard plugin structure, no custom hooks
5. **More Portable**: Works across different systems without shell-specific code

## Updating the Plugin

### Automatic Updates (Recommended)

The documentation updates automatically every 3 hours via GitHub Actions. To get these updates:

```bash
cd ~/.claude/plugins/ccdocs  # or your plugin directory
git pull
```

## Development

### Prerequisites

- Python 3.11+
- Git
- Claude Code
- [uv](https://github.com/astral-sh/uv)

### Running the Documentation Fetcher Locally

```bash
uv run scripts/fetch_claude_docs.py
```

This will fetch the latest documentation from code.claude.com and update `plugins/ccdocs/skills/docs/references/`.

### Testing the Plugin

Test without installing system-wide:

```bash
git clone https://github.com/mfreiwald/ccdocs.git
cd ccdocs
claude --plugin-dir ./plugins/ccdocs "test the ccdocs plugin"
```

## Troubleshooting

### Plugin Not Loading

**Check plugin is discoverable:**
```bash
ls -la ~/.claude/plugins/ccdocs/.claude-plugin/plugin.json
```

**Or verify --plugin-dir path:**
```bash
ls -la /path/to/ccdocs/plugins/ccdocs/.claude-plugin/plugin.json
```

### Skill Not Available

The skill should appear as `/ccdocs:docs` in skill listings. If not:

1. Verify the plugin loaded: Check Claude's startup messages
2. Check skill file: `cat plugins/ccdocs/skills/docs/SKILL.md` should have proper frontmatter
3. Restart Claude Code

### Documentation Out of Date

```bash
cd ~/.claude/plugins/ccdocs  # or your plugin directory
git fetch origin main
git pull
```

### Git Operations Failing

If "what's new" fails:

1. Ensure you're in a git repository: `git status` should work
2. Check git is installed: `git --version`
3. Verify remote is set: `git remote -v`

## Contributing

Contributions are welcome! This is a community project.

### Adding Features

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

### Reporting Issues

Found a bug or have a suggestion?

1. Check existing issues: https://github.com/mfreiwald/ccdocs/issues
2. Create a new issue with details
3. Include: Claude Code version, OS, steps to reproduce

### Documentation Improvements

Help improve the documentation or SKILL.md:

1. Edit the relevant file
2. Submit a pull request
3. Explain your improvements

## Security and Privacy

- **No external requests during normal use**: Documentation is read from local files
- **No network during normal use**: Network access only when you pull updates
- **No telemetry**: The plugin doesn't send any usage data anywhere
- **Open source**: All code is auditable on GitHub

## Credits

- **Original Documentation**: All documentation content © Anthropic
- **Official Docs Site**: https://code.claude.com/docs
- **Plugin Conversion**: Community contribution

## License

- **Plugin Code**: MIT License
- **Documentation Content**: © Anthropic (mirrored with attribution)

## Links

- **Plugin Repository**: https://github.com/mfreiwald/ccdocs
- **Official Docs**: https://code.claude.com/docs
- **Claude Code**: https://github.com/anthropics/claude-code
- **Issue Tracker**: https://github.com/mfreiwald/ccdocs/issues

## Support

Need help?

1. Check the troubleshooting section above
2. Read the official Claude Code plugin documentation: `/ccdocs:docs plugins`
3. Open an issue: https://github.com/mfreiwald/ccdocs/issues

---

**Note**: This is a community-maintained mirror of official documentation. For the most up-to-date information, always refer to the official Claude Code documentation at https://code.claude.com/docs.
