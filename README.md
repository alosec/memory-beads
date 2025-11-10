# Claude Memory + Beads Workflow System

**A personal workflow system for Claude Code featuring Memory Bank documentation, Beads issue tracking, and orchestrated subagent workflows.**

> Personal repository by [@alosec](https://github.com/alosec) - Structured documentation and issue tracking patterns for AI-assisted development.

---

## What's Included

### 📚 Memory Bank System
Structured documentation approach with shared/personal namespaces for team knowledge management. Enables fast orientation (read 3 files, ~250 lines) and knowledge graduation from exploration → validated → official docs.

### 🔗 Beads Integration
Git-native issue tracking with dependency awareness. Track issues in JSONL format alongside code, with simple export/push and import/pull workflows.

### 🤖 Orchestrated Subagents
Six specialized agents that work together in isolated contexts:
- **planning-agent** - Analyzes requirements, creates implementation plans
- **coding-agent** - Executes plans, writes code following patterns
- **testing-agent** - Deploys branches, runs E2E tests with visual validation
- **analysis-agent** - Deep analysis of complex codebase issues
- **research-agent** - Searches web for implementation examples
- **reference-agent** - Analyzes library source code in reference/ directories

### ⚡ Productivity Commands
- `/prepare` - Workspace orientation (memory bank + git + beads status)
- `/plan` - Feature planning workflow with file structure trees
- `/act` - Execute tasks and implementations
- `/analyze` - Analyze code and problems
- `/elaborate` - Elaborate on concepts and details
- `/chart` - Generate visual charts and diagrams
- `/navigate` - Codebase navigation helper
- `/define-skill` - Skill creation wizard
- `/new-window` - Window management utilities

### 🛠️ Specialized Skills
- **planning** - Feature planning with execution tasks
- **testing** - E2E testing workflow with deployment
- **quiet-build** - Filtered build output (reduces context noise)
- **quiet-deploy** - Filtered deploy output (reduces context noise)

---

## Installation

### Option 1: Install from GitHub

```bash
# Install the plugin
claude plugin install https://github.com/alosec/memory-beads.git

# Verify installation
claude plugin list
```

### Option 2: Install from Local Directory

```bash
# Clone the repository
git clone https://github.com/alosec/memory-beads.git
cd memory-beads

# Install locally
claude plugin install .

# Or symlink for development
ln -s $(pwd) ~/.claude/plugins/memory-beads
```

---

## Quick Start

### 1. Workspace Orientation

Start every session with `/prepare` to read Memory Bank, check git status, and review Beads issues:

```bash
/prepare
```

### 2. Feature Planning

Use `/plan` to create implementation plans with file structure trees:

```bash
/plan Add dark mode toggle to settings
```

This activates the **planning-agent** which will:
- Analyze existing codebase patterns
- Create a file structure tree showing [C]reate, [M]odify, [D]elete
- Generate numbered execution tasks

### 3. Orchestrated Development

The plugin enables the full orchestrated workflow:

1. **Main Claude** runs `/prepare` and `/plan`
2. **planning-agent** creates the plan
3. **coding-agent** implements the code
4. **testing-agent** deploys and tests
5. **Main Claude** commits, pushes, creates PR

---

## Memory Bank Structure

The plugin assumes this memory bank organization:

```
memory-bank/
├── shared/                    # Team knowledge
│   ├── 00-core/              # Project foundation
│   ├── 01-architecture/      # System patterns
│   ├── 02-active/            # Current work
│   ├── 03-guides/            # How-to docs
│   ├── 04-history/           # Archived sessions
│   └── 05-roadmap/           # Future planning
└── {name}/                   # Personal workspace
    ├── active/
    ├── explorations/
    └── history/
```

**Read priority:**
1. `02-active/currentWork.md` (~100 lines)
2. `02-active/blockers.md` (~50 lines)
3. `02-active/nextUp.md` (~100 lines)

**Total orientation: ~250 lines, 3-5 minutes**

---

## Beads Workflow

### Quick Commands

```bash
# Create shared/team issue (auto-discovered based on directory)
bd create "Fix sync bug" -t bug -p 0

# Create personal task (separate namespace)
bd create "Research auth patterns" -t task -p 1 --db .beads/alex/alex.db

# Update status (shared issues)
bd update project-123 --status in_progress

# Git workflow (auto-export via hooks)
git add .
git commit -m "beads: update project-123"  # Hooks auto-export to .beads/issues.jsonl
git push

# After pull - import to sync
git pull
bd import  # Reads from .beads/issues.jsonl automatically
```

### Two-Tier Tracking

| Tier | Prefix | Database Path | Use Case |
|------|--------|---------------|----------|
| **Shared** | `{project}-*` | `.beads/{project}.db` | Team-visible work (auto-discovered) |
| **Personal** | `{name}-*` | `.beads/{name}/{name}.db` | Personal todos (explicit --db flag) |

**Key Pattern:**
- **Shared issues**: Use Beads auto-discovery (`.beads/{project}.db` + `.beads/issues.jsonl`)
- **Personal tasks**: Use explicit `--db` flag to avoid crowding shared issue space

---

## Agent Usage

### Planning Agent

Automatically activates when you use `/plan` or ask Claude to create an implementation plan:

```
I need to add user authentication to the app
```

Returns:
- Clear understanding of requirements
- File structure tree with [C]/[M]/[D] annotations
- Numbered execution tasks

### Coding Agent

Orchestrator delegates implementation work:

```
Implement the authentication plan we just created
```

The coding-agent:
- Follows the plan step-by-step
- Uses TodoWrite to track progress
- Returns implementation summary

### Testing Agent

Orchestrator delegates testing:

```
Deploy and test the authentication feature
```

The testing-agent:
- Deploys feature branch
- Runs E2E tests against deployment
- Tails logs for debugging
- Returns test results

---

## Skills Reference

### Planning Skill

**Activation:** User asks to plan, analyze, design, or create implementation plan

**Workflow:**
1. Quick code review (2-3 minutes)
2. Analyze request
3. Simple understanding (2-3 sentences)
4. File structure plan (ASCII tree)
5. Execution tasks (numbered list)

### Testing Skill

**Activation:** User mentions testing, e2e tests, test deployment, visual testing

**Workflow:**
1. Deploy feature branch to get live URL
2. Run Playwright tests
3. Capture screenshots at mobile/tablet/desktop
4. Email report with visual evidence
5. Iterate based on feedback

### Quiet Build/Deploy Skills

**Activation:** User mentions building or deploying

**Benefit:** Filters verbose output to show only errors and key status messages, reducing context usage during CI operations.

---

## Configuration

### Setting Up Memory Bank

1. Create the directory structure:
```bash
mkdir -p memory-bank/{shared,alex}/{00-core,01-architecture,02-active,03-guides,04-history,05-roadmap}
```

2. Initialize core files:
```bash
touch memory-bank/shared/02-active/{currentWork,blockers,nextUp}.md
touch memory-bank/alex/active/{currentWork,blockers,nextUp}.md
```

3. Start with `/prepare` to verify structure

### Setting Up Beads

1. Initialize databases:
```bash
# Shared/team issues (auto-discovered by directory name)
bd init --prefix myproject

# Personal tasks (separate namespace)
bd init --prefix alex --db .beads/alex/alex.db
```

2. Add to `.gitignore`:
```gitignore
# Ignore all .db files
.beads/*.db
.beads/*/*.db

# Track the shared issues JSONL
!.beads/issues.jsonl
```

3. Install git hooks (optional - auto-export on commit):
```bash
bd hooks install
```

---

## Development

### Testing the Plugin Locally

```bash
# Run with debug logging
claude --debug

# Verify plugin loaded
claude plugin list

# Test a command
/prepare

# Test agent activation
/plan Add a new feature
```

### Updating the Plugin

```bash
# Pull latest changes
cd ~/.claude/plugins/memory-beads
git pull

# Reload Claude Code
claude plugin reload memory-beads
```

---

## Requirements

- **Claude Code**: >= 1.0.0
- **Beads CLI**: Install from [github.com/steveyegge/beads](https://github.com/steveyegge/beads)
- **Git**: For version control and plugin installation

---

## License

MIT License - see [LICENSE](./LICENSE) file for details

---

## Contributing

This is a personal workflow system. Feel free to fork and adapt for your own use! For issues or suggestions:

1. Open a GitHub issue
2. Or fork the repo and submit a PR
3. Share your own workflow patterns!

---

## Support

For questions or issues:
- Check plugin loading: `claude --debug`
- Verify structure: `ls -la ~/.claude/plugins/memory-beads/`
- Review agent definitions in `agents/`
- Check command syntax in `commands/`
- Open an issue on GitHub

**Happy coding!** 🚀
