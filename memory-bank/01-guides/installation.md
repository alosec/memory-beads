# Installation Guide

**Plugin:** memory-beads v0.1.0
**Last Updated:** 2025-11-08

---

## Prerequisites

Before installing the plugin, ensure you have:

- **Claude Code** >= 1.0.0 installed
- **Git** for version control
- **Beads CLI** installed (optional but recommended for issue tracking)
- **GitHub account** with SSH key configured (for private repo access)

---

## Installation Methods

### Method 1: Install from GitHub (Recommended)

Install directly from the private ideaflow repository:

```bash
# Install the plugin
claude plugin install git@github.com:ideaflow/memory-beads.git

# Verify installation
claude plugin list
```

You should see `memory-beads` in the output.

### Method 2: Install from Local Directory

Useful for development or if you've cloned the repo:

```bash
# Clone the repository
git clone git@github.com:ideaflow/memory-beads.git ~/code/plugins/memory-beads

# Install from local path
cd ~/code/plugins/memory-beads
claude plugin install .

# Or create a symlink (for active development)
ln -s ~/code/plugins/memory-beads ~/.claude/plugins/memory-beads
```

---

## Post-Installation Setup

### 1. Verify Plugin Loaded

```bash
# Check for the plugin
claude plugin list

# Test a command
claude
> /prepare

# Test skill activation (in a project directory)
claude
> Plan a new feature for user authentication
```

If the planning skill activates or `/prepare` runs successfully, the plugin is working!

### 2. Install Beads CLI (Optional)

For full functionality with Beads issue tracking:

```bash
# Install Beads (check https://github.com/beadslabs/beads for latest)
cargo install beads

# Or download binary from releases
# https://github.com/beadslabs/beads/releases

# Verify installation
bd --version
```

### 3. Set Up Your Project

Initialize Memory Bank and Beads in your project:

```bash
cd ~/code/your-project

# Create Memory Bank structure
mkdir -p memory-bank/{shared,yourname}/{00-core,01-architecture,02-active,03-guides,04-history,05-roadmap}

# Create core active files
touch memory-bank/shared/02-active/{currentWork,blockers,nextUp}.md
touch memory-bank/yourname/02-active/{currentWork,blockers,nextUp}.md

# Initialize Beads for shared project issues
mkdir -p .beads/shared
bd init --prefix yourproject --db .beads/shared/yourproject.db

# Initialize Beads for personal tasks
mkdir -p .beads/yourname
bd init --prefix yourname --db .beads/yourname/yourname.db

# Add to .gitignore
echo "*.db" >> .beads/.gitignore

# Track JSONL exports
git add .beads/shared/issues.jsonl .beads/yourname/issues.jsonl
```

---

## Configuration

### Global Claude Settings

Optional: Add Beads aliases to your shell config (~/.bashrc or ~/.zshrc):

```bash
# Shared project issues
alias bdp='bd --db .beads/shared/yourproject.db'

# Personal tasks
alias bdm='bd --db .beads/yourname/yourname.db'
```

Reload your shell:
```bash
source ~/.bashrc  # or ~/.zshrc
```

### Project-Specific Setup

Create a project `.clauderules` file for consistency:

```markdown
# .clauderules

## Memory Bank

This project uses the Memory Bank system from the memory-beads plugin.

**Quick orientation:**
- Read `memory-bank/shared/02-active/currentWork.md`
- Check `memory-bank/shared/02-active/blockers.md`
- Review `memory-bank/shared/02-active/nextUp.md`

## Beads Issue Tracking

We track issues with Beads:
- Shared issues: `.beads/shared/yourproject.db` (prefix: yourproject-*)
- Personal tasks: `.beads/{name}/{name}.db` (prefix: {name}-*)

**Workflow:**
1. Export before push: `bd export --db .beads/shared/yourproject.db -o .beads/shared/issues.jsonl`
2. Import after pull: `bd import --db .beads/shared/yourproject.db -i .beads/shared/issues.jsonl`

## Commands

Use these commands for efficient workflow:
- `/prepare` - Orient to workspace (memory-bank + git + beads)
- `/plan` - Create implementation plan for features
```

---

## Verification

### Test the Complete Workflow

1. **Start a session:**
```bash
cd ~/code/your-project
claude
> /prepare
```

You should see:
- Memory Bank summary (currentWork, blockers, nextUp)
- Git status
- GitHub account check
- Beads issues summary

2. **Plan a feature:**
```bash
> /plan Add a dark mode toggle to settings
```

You should see:
- Understanding of requirements
- File structure tree
- Numbered execution tasks

3. **Create an issue:**
```bash
# Outside Claude
bd create "Implement dark mode" -t feature -p 1 --db .beads/shared/yourproject.db
bd export --db .beads/shared/yourproject.db -o .beads/shared/issues.jsonl
git add .beads/shared/issues.jsonl
git commit -m "beads: created yourproject-1"
```

4. **Re-run /prepare:**
```bash
> /prepare
```

The Beads summary should now show your new issue!

---

## Troubleshooting

### Plugin Not Loading

**Check installation:**
```bash
claude plugin list
```

If `memory-beads` isn't listed:
```bash
# Reinstall
claude plugin install git@github.com:ideaflow/memory-beads.git

# Or check for errors
claude --debug
```

**Check plugin.json:**
```bash
cat ~/.claude/plugins/memory-beads/.claude-plugin/plugin.json
```

Verify it has valid JSON and proper paths.

### Commands Not Working

**/prepare or /plan not found:**

Check that commands are properly listed in plugin.json:
```bash
grep -A 5 '"commands"' ~/.claude/plugins/memory-beads/.claude-plugin/plugin.json
```

Should show paths to `./commands/prepare.md` and `./commands/plan.md`.

### Agents Not Activating

**planning-agent or testing-agent not running:**

1. Check agent files exist:
```bash
ls ~/.claude/plugins/memory-beads/agents/
```

2. Verify YAML frontmatter:
```bash
head -n 10 ~/.claude/plugins/memory-beads/agents/planning-agent.md
```

Should have valid `---` delimited frontmatter with `name` and `description`.

### Beads Issues

**bd command not found:**

Install Beads CLI (see Post-Installation Setup above).

**Database errors:**

Make sure you're using the correct `--db` path:
```bash
# Shared issues
bd list --db .beads/shared/yourproject.db

# Personal tasks
bd list --db .beads/yourname/yourname.db
```

---

## Updating the Plugin

### Pull Latest Changes

```bash
cd ~/.claude/plugins/memory-beads
git pull

# Reload plugin
claude plugin reload memory-beads
```

### Check for Updates

```bash
# See current version
grep version ~/.claude/plugins/memory-beads/.claude-plugin/plugin.json

# Check remote for new releases
cd ~/.claude/plugins/memory-beads
git fetch
git log HEAD..origin/main --oneline
```

---

## Uninstallation

### Remove the Plugin

```bash
# Uninstall
claude plugin uninstall memory-beads

# Or manually remove
rm -rf ~/.claude/plugins/memory-beads
```

### Clean Up Project Files (Optional)

If you want to remove Memory Bank and Beads from a project:

```bash
cd ~/code/your-project

# Remove Memory Bank
rm -rf memory-bank/

# Remove Beads
rm -rf .beads/

# Remove from git
git rm -r memory-bank/ .beads/
git commit -m "Remove memory-beads workflow system"
```

---

## Next Steps

After installation, check out:

- **[agent-usage.md](./agent-usage.md)** - How to work with subagents
- **[customization.md](./customization.md)** - Adapt the plugin for your team
- **[../02-active/currentWork.md](../02-active/currentWork.md)** - Current plugin development status

---

## Support

**Issues or questions?**

1. Check the plugin's memory-bank: `~/.claude/plugins/memory-beads/memory-bank/`
2. Review example setup in `.beads/` and `memory-bank/` directories
3. Run `claude --debug` to see detailed loading information
4. Create a Beads issue in your project and discuss with the team

**Happy coding!** 🚀
