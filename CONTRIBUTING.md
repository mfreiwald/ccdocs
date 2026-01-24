# Contributing to Claude Code Documentation Plugin

Thank you for your interest in contributing to the Claude Code Documentation Plugin! This document provides guidelines for contributing.

## Ways to Contribute

### 1. Report Bugs

Found a bug? Please [open an issue](https://github.com/mfreiwald/ccdocs/issues) with:

- **Clear title**: Summarize the issue in one line
- **Description**: Detailed explanation of the problem
- **Steps to reproduce**: How to trigger the bug
- **Expected behavior**: What should happen
- **Actual behavior**: What actually happens
- **Environment**: OS, Claude Code version, plugin version
- **Logs/screenshots**: Any relevant error messages

### 2. Suggest Features

Have an idea for improvement? Create an issue with:

- **Use case**: Why is this feature needed?
- **Proposed solution**: How should it work?
- **Alternatives**: Other approaches you've considered
- **Examples**: Similar features in other tools

### 3. Improve Documentation

Documentation improvements are always welcome:

- Fix typos or unclear explanations
- Add examples or use cases
- Improve README or SKILL.md clarity
- Add troubleshooting tips

### 4. Contribute Code

Ready to code? Follow these steps:

#### Setting Up Development Environment

1. **Fork the repository**
   ```bash
   # Via GitHub UI, then:
   git clone https://github.com/YOUR_USERNAME/ccdocs.git
   cd ccdocs
   ```

2. **Create a feature branch**
   ```bash
   git checkout -b feature/your-feature-name
   # or
   git checkout -b fix/bug-description
   ```

3. **Install uv**
   See https://github.com/astral-sh/uv for installation options.

4. **Test the plugin**
   ```bash
   claude --plugin-dir ./plugins/ccdocs "test /ccdocs:docs"
   ```

#### Making Changes

**For SKILL.md changes:**
- Edit `plugins/ccdocs/skills/docs/SKILL.md`
- Test thoroughly with various argument patterns
- Ensure all 51 docs remain accessible
- Verify error handling works correctly

**For fetch script changes:**
- Edit `scripts/fetch_claude_docs.py`
- Test locally: `uv run scripts/fetch_claude_docs.py`
- Verify manifest updates correctly
- Check that all 51 docs + changelog are fetched

**For workflow changes:**
- Edit `.github/workflows/update-docs.yml`
- Test with workflow_dispatch trigger
- Verify commits are formatted correctly
- Ensure error handling creates issues

#### Testing Your Changes

1. **Test basic functionality**
   ```bash
   claude --plugin-dir ./plugins/ccdocs "/ccdocs:docs"
   claude --plugin-dir ./plugins/ccdocs "/ccdocs:docs hooks"
   claude --plugin-dir ./plugins/ccdocs "/ccdocs:docs what's new"
   ```

2. **Test error cases**
   ```bash
   claude --plugin-dir ./plugins/ccdocs "/ccdocs:docs nonexistent-topic"
   ```

3. **Test with different scenarios**
   - Fresh git repository
   - With uncommitted changes
   - Offline (network unavailable)
   - After docs updates

4. **Verify documentation fetch**
   ```bash
   python scripts/fetch_claude_docs.py
   git diff plugins/ccdocs/skills/docs/references/
   ```

#### Code Style

- **Python**: Follow PEP 8, use type hints where helpful
- **Markdown**: Use consistent formatting, check links work
- **YAML**: Use 2-space indentation for GitHub Actions
- **Comments**: Explain "why" not "what"

#### Commit Messages

Use clear, descriptive commit messages:

```
Add support for fuzzy topic matching

- Implement Levenshtein distance for topic suggestions
- Show top 3 matches when topic not found
- Add tests for fuzzy matching logic

Closes #42
```

Format:
- First line: Short summary (50 chars or less)
- Blank line
- Detailed description (wrap at 72 chars)
- Reference issues/PRs

#### Submitting a Pull Request

1. **Push your branch**
   ```bash
   git push origin feature/your-feature-name
   ```

2. **Create pull request**
   - Go to GitHub and click "New Pull Request"
   - Fill in the template with details
   - Link related issues
   - Request review

3. **Address feedback**
   - Respond to review comments
   - Make requested changes
   - Push updates to your branch

4. **Wait for approval**
   - Maintainers will review and merge
   - Or request additional changes

## Development Guidelines

### Plugin Architecture

The plugin uses a skill-based architecture:

```
User Input → /ccdocs:docs skill → Claude interprets SKILL.md
                                  ↓
                          Uses allowed tools (Read, Bash)
                                  ↓
                          Accesses references/ directory
                                  ↓
                          Formats and presents output
```

### Key Design Principles

1. **Simplicity**: Keep the skill logic straightforward
2. **Robustness**: Handle errors gracefully, never crash
3. **Offline-first**: Work without network for normal usage
4. **Attribution**: Always show original documentation sources
5. **Performance**: Minimize tool calls, use caching where possible

### File Organization

- **plugins/ccdocs/skills/docs/SKILL.md**: All skill logic and instructions for Claude
- **plugins/ccdocs/skills/docs/docs_manifest.json**: Metadata about all documentation files
- **plugins/ccdocs/skills/docs/references/**: Documentation files (auto-updated, minimize manual edits)
- **scripts/**: Tools for maintaining the plugin
- **.github/workflows/**: Automation for documentation sync

### Testing Checklist

Before submitting a PR, verify:

- [ ] Plugin loads without errors
- [ ] All 51 documentation topics are accessible
- [ ] `/ccdocs:docs` lists all topics correctly
- [ ] `/ccdocs:docs <topic>` reads documentation
- [ ] `/ccdocs:docs what's new` shows changes
- [ ] `/ccdocs:docs changelog` displays release notes
- [ ] Error messages are helpful and actionable
- [ ] Documentation is up to date
- [ ] No broken links in README or docs
- [ ] Commit messages are clear

## Documentation Updates

The documentation in `plugins/ccdocs/skills/docs/references/` is auto-updated via GitHub Actions. Generally, don't edit these files manually unless:

1. **Fixing fetch errors**: If the fetch script missed something
2. **Emergency updates**: Critical documentation fixes
3. **Testing**: Verifying changes work correctly

For manual edits:
- Update `docs_manifest.json` to reflect changes
- Note that auto-updates will overwrite manual changes
- Consider fixing the fetch script instead

## GitHub Actions Workflow

The plugin auto-updates documentation every 3 hours:

1. **Trigger**: Schedule (every 3 hours) or manual dispatch
2. **Fetch**: Runs `scripts/fetch_claude_docs.py`
3. **Check**: Detects if any docs changed
4. **Commit**: Creates commit with changed file list
5. **Push**: Updates main branch
6. **Error handling**: Creates issue if fetch fails

To test workflow changes:
1. Make changes to `.github/workflows/update-docs.yml`
2. Push to your branch
3. Trigger workflow manually via Actions tab
4. Verify it works correctly

## Code Review Process

Maintainers will review PRs for:

1. **Functionality**: Does it work as intended?
2. **Quality**: Is the code clean and maintainable?
3. **Testing**: Has it been tested thoroughly?
4. **Documentation**: Are changes documented?
5. **Compatibility**: Does it work on all platforms?

Reviews typically take 1-3 days. Be patient and responsive to feedback.

## Community Guidelines

- **Be respectful**: Treat everyone with kindness
- **Be constructive**: Provide helpful feedback
- **Be patient**: Maintainers are volunteers
- **Be collaborative**: Work together towards solutions
- **Give credit**: Acknowledge others' contributions

## Questions?

- **General questions**: Open a discussion on GitHub
- **Bug reports**: Create an issue
- **Feature requests**: Create an issue with [Feature Request] tag
- **Security issues**: Email maintainers (see SECURITY.md)

## Recognition

Contributors are acknowledged in:
- GitHub contributors page
- Release notes (for significant contributions)
- README (for major features)

Thank you for contributing to make this plugin better!

---

**Quick Links:**
- [Issues](https://github.com/mfreiwald/ccdocs/issues)
- [Pull Requests](https://github.com/mfreiwald/ccdocs/pulls)
- [Discussions](https://github.com/mfreiwald/ccdocs/discussions)
- [README](README.md)
- [License](LICENSE)
