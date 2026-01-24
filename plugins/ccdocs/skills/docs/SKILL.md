---
name: docs
description: Access Claude Code documentation locally. Supports reading docs, viewing changes, and reading the changelog. Use when answering questions about Claude Code features, configuration, or usage.
allowed-tools: Read, Bash, Grep, Glob
---

# Claude Code Documentation Access

This skill provides access to locally mirrored Claude Code documentation from the official docs site. Documentation is stored in the `references/` directory and automatically syncs via GitHub Actions every 3 hours.

## Documentation Location

All documentation files are in the `references/` subdirectory of this skill:
- **Documentation files**: `references/*.md` (51 files total)
- **Metadata manifest**: `docs_manifest.json`

The manifest contains metadata for all documentation files including:
- Original URLs on code.claude.com
- Content hashes for change detection
- Last update timestamps

All file paths in this skill are relative to the skill directory.

## Available Arguments

Process `$ARGUMENTS` to determine what action to take:

- **(no arguments or empty)**: List all available documentation topics with descriptions
- **`<topic>`**: Read specific documentation file (e.g., `hooks`, `mcp`, `skills`, `plugins`)
- **`what's new`** or **`whats new`**: Show recent documentation changes from git history
- **`changelog`**: Read the official Claude Code changelog (release notes)
- **`uninstall`**: Show uninstall instructions

## Available Documentation Topics

The following 51 topics are available (from docs_manifest.json):

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

## Instructions for Each Request Type

### 1. List All Topics (No Arguments)

When `$ARGUMENTS` is empty or just whitespace:

1. Read `docs_manifest.json` (relative to skill directory)
2. Extract all file keys (51 total)
3. Display organized list with these sections:
   - Header with plugin info and source attribution
   - Topics grouped by category (use the categories from the list above)
   - Show total count: "51 topics available"
   - Show last sync info from `fetch_metadata.last_fetch_completed`
4. Include usage examples at the end

**Example output format:**
```
📚 Claude Code Documentation (Local Mirror)
🔗 Official Docs: https://code.claude.com/docs
🔗 Mirror Repo: https://github.com/mfreiwald/ccdocs

Available Topics (51 total):

Core Documentation:
  overview, quickstart, features-overview, how-claude-code-works
  setup, cli-reference, common-workflows, best-practices
  interactive-mode, headless

[... other categories ...]

Last synced: [timestamp from manifest]

Usage:
  /ccdocs:docs <topic>        - Read specific documentation
  /ccdocs:docs what's new     - Show recent changes
  /ccdocs:docs changelog      - Read Claude Code release notes
```

### 2. Read Specific Topic

When `$ARGUMENTS` contains a topic name (e.g., `hooks`, `mcp`, `skills`):

1. **Sanitize the topic name**:
   - Convert to lowercase
   - Remove any path separators or special characters
   - Ensure it's just a simple filename without extension

2. **Check if file exists**:
   - Look for `references/<topic>.md` (relative to skill directory)
   - If not found, suggest similar topics using fuzzy matching

3. **Read and display the documentation**:
   ```
   📚 Claude Code Documentation: <Title>
   🔗 Official: <URL from manifest>

   [Full markdown content of the doc]

   ---
   📖 Official page: <URL from manifest>
   🕐 Last updated: <timestamp from manifest>
   ```

### 3. What's New

When `$ARGUMENTS` contains "what's new" or "whats new":

1. **Get recent changes from git log** (from plugin root):

   Navigate to plugin root (2 levels up from skill directory):
   ```bash
    cd ../.. && git log --since="7 days ago" \
     --pretty=format:"%h|%ar|%s" --name-status -- skills/docs/references/ | head -50
   ```

2. **Parse and format the output**:
   ```
   📚 Recent Documentation Updates (Last 7 Days)

   • [time ago]:
     📎 Commit: https://github.com/mfreiwald/ccdocs/commit/[hash]
     📄 [filename]: https://code.claude.com/docs/en/[filename without .md]
        [Change description from commit message]

   [Repeat for each commit]

   ---
   📎 Full history: https://github.com/mfreiwald/ccdocs/commits/main/plugins/ccdocs/skills/docs/references
   🔄 Auto-updates run every 3 hours
   ```

3. **If no changes in last 7 days**:
   ```
   📚 No documentation updates in the last 7 days

   Last update: <most recent commit date>
   Check full history: https://github.com/mfreiwald/ccdocs/commits/main/plugins/ccdocs/skills/docs/references
   ```

### 4. Changelog

When `$ARGUMENTS` is "changelog":

1. **Read the changelog file**:
   - File: `references/changelog.md`

2. **Display with proper attribution**:
   ```
   📚 Claude Code Changelog (Official Release Notes)
   🔗 Source: https://github.com/anthropics/claude-code/blob/main/CHANGELOG.md

   [Full changelog content]

   ---
   📖 This changelog is from the official claude-code repository
   🕐 Last synced: <timestamp from manifest for changelog.md>

   💡 Tip: Run `/ccdocs:docs what's new` to see documentation updates
   ```

### 5. Uninstall Instructions

When `$ARGUMENTS` is "uninstall":

Display:
```
🗑️  Uninstalling ccdocs Plugin

To remove this plugin:

1. Remove the plugin directory:
   rm -rf ~/.claude/plugins/ccdocs

2. Or if using --plugin-dir:
   Simply stop using the --plugin-dir flag

The plugin can be reinstalled anytime from:
https://github.com/mfreiwald/ccdocs
```

## Error Handling

### Topic Not Found

When a requested topic doesn't exist:

1. Search for similar topics (fuzzy match on topic name)
2. Display helpful error:
   ```
   ❌ Documentation topic not found: "<topic>"

   Did you mean one of these?
     • <similar-topic-1>
     • <similar-topic-2>
     • <similar-topic-3>

   Run `/ccdocs:docs` to see all available topics.
   ```

### File Read Errors

If unable to read a documentation file:

```
❌ Unable to read documentation file: references/<topic>.md

This might indicate a plugin installation issue.
Try reinstalling the plugin or check the GitHub repository.
```

### Git Operation Errors

If git commands fail (for what's new):

```
⚠️  Unable to check recent changes - git operation failed

This might happen if:
  • You're offline
  • The plugin directory is not a git repository
  • Git is not installed

The documentation is still available locally.
```

## Best Practices

1. **Always show source attribution**: Include links to official docs and GitHub
2. **Preserve formatting**: Display markdown documentation as-is
3. **Be helpful with errors**: Suggest alternatives when topics aren't found
4. **Show context**: Include timestamps and update information
5. **Link to originals**: Always provide the official documentation URL

## Technical Notes

- The manifest is the source of truth for all available topics
- Documentation syncs automatically via GitHub Actions (see `.github/workflows/update-docs.yml` at repository root)
- Repository maintenance scripts are in `scripts/` at repository root (not part of the skill)
- The plugin works offline; "what's new" uses local git history
- Documentation updates are automatic via GitHub Actions (no manual refresh command)
- Git operations should be non-blocking and gracefully handle failures
- All 51 documentation files are available immediately without external fetches
- Documentation file paths are relative to the skill directory (`references/<topic>.md`)
- Git operations must run from plugin root (navigate up 2 levels: `cd ../..`)

## Example Interactions

**List topics:**
```
User: /ccdocs:docs
Claude: [Shows categorized list of all 51 topics with descriptions]
```

**Read specific doc:**
```
User: /ccdocs:docs hooks
Claude: [Displays hooks.md content with official URL and timestamp]
```

**See what changed:**
```
User: /ccdocs:docs what's new
Claude: [Lists recent commits with changed files and links]
```

**Read changelog:**
```
User: /ccdocs:docs changelog
Claude: [Displays Claude Code release notes from changelog.md]
```

---

## Implementation Notes for Claude

When executing this skill:

1. **Parse $ARGUMENTS** first to determine the action
2. **Use the Read tool** for documentation files and manifest (use relative paths from skill directory)
3. **Use Bash tool** sparingly - only for git operations (must cd to plugin root first)
4. **Always attribute sources** - show both mirror and official URLs
5. **Handle errors gracefully** - network/git failures shouldn't break the plugin
6. **Be consistent with formatting** - use the emoji and formatting conventions shown above

**Path handling**:
- Documentation files: `references/<topic>.md` (relative to skill directory)
- Manifest: `docs_manifest.json` (relative to skill directory)
- Git operations: Must run from plugin root (navigate up: `cd ../..` from skill directory)
