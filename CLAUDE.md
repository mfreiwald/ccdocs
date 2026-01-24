# Claude Code Docs Marketplace

This repository is a Claude Code marketplace that ships the `ccdocs` plugin, which provides the `/ccdocs:docs` skill for accessing local Claude Code documentation.

## Plugin Structure

The marketplace contains the `ccdocs` plugin under `plugins/ccdocs/`, with repository infrastructure at the root:

**Skill files** (what Claude reads during skill execution):
- `plugins/ccdocs/skills/docs/SKILL.md` - Main skill definition that instructs Claude how to access docs
- `plugins/ccdocs/skills/docs/references/` - Contains 51 documentation markdown files
- `plugins/ccdocs/skills/docs/docs_manifest.json` - Metadata manifest for all documentation files

**Repository infrastructure** (for maintaining the marketplace):
- `.claude-plugin/marketplace.json` - Marketplace manifest defining available plugins
- `plugins/ccdocs/.claude-plugin/plugin.json` - Plugin manifest defining name, version, and metadata
- `scripts/fetch_claude_docs.py` - Python script that fetches latest docs from code.claude.com
- `.github/workflows/update-docs.yml` - GitHub Actions workflow that auto-updates docs every 3 hours

## How It Works

1. **Documentation Storage**: All 51 Claude Code documentation files are stored locally in `plugins/ccdocs/skills/docs/references/`
2. **Metadata Tracking**: `plugins/ccdocs/skills/docs/docs_manifest.json` tracks file hashes, URLs, and last update times
3. **Skill-based Access**: The `/ccdocs:docs` skill parses user arguments and uses Read/Bash tools to access docs
4. **Auto-sync**: GitHub Actions fetches latest docs every 3 hours and commits changes
5. **Change Tracking**: Uses git history to show recent documentation updates

## Usage Examples

- `/ccdocs:docs` - List all 51 available topics
- `/ccdocs:docs hooks` - Read hooks documentation
- `/ccdocs:docs what's new` - Show recent documentation changes
- `/ccdocs:docs changelog` - Read Claude Code release notes

## Development Notes

- Documentation comes from https://code.claude.com/docs (scraped via sitemap)
- Changelog comes from https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md
- The fetch script uses the requests library to download markdown versions
- All documentation content © Anthropic, mirrored with attribution

## Testing

Test the plugin without installation:
```bash
claude --plugin-dir /path/to/ccdocs/plugins/ccdocs "test /ccdocs:docs"
```

## Comparison to Standalone Version

This marketplace replaces the earlier standalone claude-code-docs tool with:
- Simpler installation (just git clone)
- Native plugin architecture (no hooks or custom commands)
- Better namespacing (/ccdocs:docs vs /docs)
- Same auto-update functionality via GitHub Actions

## Files to Understand

When working on this plugin:

**Skill files** (Claude reads these during execution):
1. **plugins/ccdocs/skills/docs/SKILL.md** - Core logic for how Claude handles documentation requests
2. **plugins/ccdocs/skills/docs/docs_manifest.json** - Metadata about all documentation files
3. **plugins/ccdocs/skills/docs/references/*.md** - All 51 documentation files

**Repository infrastructure** (for maintaining the marketplace):
4. **scripts/fetch_claude_docs.py** - Documentation fetching and updating logic
5. **.github/workflows/update-docs.yml** - Auto-update workflow configuration
6. **README.md** - User-facing documentation

The skill directory (`plugins/ccdocs/skills/docs/`) contains only files that Claude accesses during skill execution. Infrastructure for maintaining the marketplace lives at the repository root.

## Maintenance

The plugin is designed to be low-maintenance:
- GitHub Actions handles documentation updates automatically
- No dependencies except Python and git
- No runtime dependencies for users (Claude handles everything)
