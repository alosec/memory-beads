# Claude Memory + Beads Workflow System

**A personal workflow system for Claude Code featuring Memory Bank documentation, Beads issue tracking, and strategic subagent delegation for token efficiency.**

> Personal repository by [@alosec](https://github.com/alosec) - Structured documentation and issue tracking patterns for AI-assisted development.

---

## Philosophy

**Fast context loading → Informed work → Close the loop**

This system enables Claude to reach **80% context in under 10 tool calls** via Memory Bank + Beads, then work efficiently directly in the main thread or optionally delegate with strategic subagent calls to keep the main thread clean (especially for token-heavy operations like Playwright testing).

---

## The Core Workflow

### 1. **`/prepare`** - Load Context Fast

Orient to the workspace in seconds:
- **Memory Bank**: Read 3 files (~250 lines) - `currentWork.md`, `blockers.md`, `nextUp.md`
- **Git status**: Check uncommitted changes, recent commits
- **GitHub account**: Verify correct account with `gh auth status`
- **Beads issues**: See what's ready to work on with `bd ready`

**Goal**: 80% general context in <10 tool calls, ready for informed work.

### 2. **Pick or Create Work** (Optional)

Already have a Beads issue? Great. Need to create one? Do it now or during `/chart`.

### 3. **`/plan`** - Understand What to Build

Quick codebase review and create implementation plan:
- Analyze requirements (2-3 sentence understanding)
- File structure tree with `[C]reate`, `[M]odify`, `[D]elete` annotations
- Numbered execution tasks

**Optional**: `/elaborate` for file-level implementation details (specific line changes, exact modifications).

### 4. **`/act`** - Execute the Plan

Transition from planning to implementation:
- Reviews conversation context (plan, decisions, insights)
- Creates TodoWrite list if needed
- Implements changes systematically
- Marks todos as in_progress → completed

### 5. **Review & Test**

Validate the work through multiple lenses:

**Manual Review:**
- Dev server (rapid iteration, visual feedback)
- Preview deployment (real environment, auth, edge functions)

**Test-Aware Development:**
- Write failing tests to capture intent ("Login should redirect to /dashboard")
- Run existing tests to prevent regressions
- **The delicate dance**: Tests slow down early exploration, speed up later refinement
- Balance manual iteration (feels right?) vs automated validation (works right?)

**MCP-Assisted Exploration:**
- Use Playwright MCP on dev/deployed URLs
- Explore edge cases interactively
- Discover issues that inform test creation

**Formalized Testing:**
- Turn manual journeys into E2E tests
- **Use testing-agent for token efficiency** (isolates Playwright overhead)
  - Without agent: 10,000+ tokens (screenshots as base64, DOM snapshots)
  - With agent: ~300 tokens (summary: "✅ Works on desktop/mobile, ❌ tablet button not visible")

### 6. **`/chart`** - Close the Loop

Update all tracking systems comprehensively:

**Beads Issue Management:**
- `bd create "Title" -t bug|feature|task -p 0-4` (create new issues)
- `bd update <id> --status in_progress|pending|blocked` (update progress)
- `bd close <id> -r "Resolution summary"` (close completed work)

**Memory Bank Updates:**
- `currentWork.md` - Update current focus, mark completions
- `blockers.md` - Add new blockers or move resolved ones
- `nextUp.md` - Adjust priorities based on discoveries

**Cross-Reference:** Link Beads IDs in Memory Bank, reference Memory Bank context in Beads issues.

**Why this matters**: Next session's `/prepare` loads clean, accurate context. Nothing falls through cracks.

### 7. **Save to Feature Branch**

Standard git workflow:
- Commit changes with clear message
- Push to feature branch
- Create PR if ready for review

---

## What's Included

### 📚 Memory Bank System

**Structured documentation for fast orientation:**

```
memory-bank/
├── 00-core/              # Project foundation (what/why)
├── 01-architecture/      # System patterns (how it's built)
├── 02-active/            # Current work (what's happening NOW) ← START HERE
├── 03-guides/            # How-to docs (implementation reference)
├── 04-history/           # Archived sessions (completed work)
└── 05-roadmap/           # Future planning
```

**Read priority for `/prepare`:**
1. `02-active/currentWork.md` (~100 lines) - Current focus
2. `02-active/blockers.md` (~50 lines) - Active obstacles
3. `02-active/nextUp.md` (~100 lines) - Prioritized tasks

**Total orientation: ~250 lines, 3-5 minutes**

### 🔗 Beads Integration

**Git-native issue tracking with dependency awareness:**

```bash
# Shared/team issues (auto-discovered)
bd create "Fix sync bug" -t bug -p 0
bd update project-123 --status in_progress
bd close project-456 -r "Fixed session cookie handling"

# Personal tasks (separate namespace)
bd create "Research auth patterns" -t task -p 1 --db .beads/alex/alex.db

# Git workflow (auto-export via hooks)
git commit -m "feat: add dark mode"  # Auto-exports to .beads/issues.jsonl
git push

# After pull - sync
git pull
bd import  # Reads from .beads/issues.jsonl
```

**Two-tier tracking:**
- **Shared** (`.beads/{project}.db` + `.beads/issues.jsonl`) - Team-visible work
- **Personal** (`.beads/{name}/{name}.db`) - Individual todos (explicit `--db` flag)

### ⚡ Core Commands

Commands that drive the workflow:

- **`/prepare`** - Workspace orientation (memory bank + git + beads)
- **`/plan`** - Create implementation plan with file structure tree
- **`/elaborate`** - Get file-level implementation details
- **`/act`** - Execute the plan systematically
- **`/analyze`** - Examine codebase using subagent analysis
- **`/chart`** - Update Beads issues + Memory Bank comprehensively
- **`/navigate`** - Codebase navigation helper
- **`/define-skill`** - Skill creation wizard
- **`/new-window`** - Window management utilities

### 🤖 Strategic Subagent Delegation

**Optional optimization for token-heavy operations** - not a rigid requirement, use when context efficiency matters:

#### Testing Agent (Most Valuable)

**Problem:** Playwright MCP generates massive token overhead
- Screenshots = 2000 tokens each (base64 images)
- DOM snapshots = 1500 tokens each
- Typical test journey = 10,000+ tokens in main context

**Solution:** Delegate to testing-agent
- Agent does Playwright work in isolated context
- Returns concise summary: "✅ Dark mode works on desktop/mobile, ❌ Toggle button not visible on tablet - screenshot shows overflow issue"
- **Saves 95% of tokens in main thread**

**When to use:**
- Running E2E tests with Playwright MCP
- Visual validation across multiple breakpoints
- Debugging UI issues with screenshots/console logs

#### Other Agents (Situational)

Available when deep exploration would clutter main context:

- **planning-agent** - Complex planning requiring extensive codebase analysis
- **coding-agent** - Large implementations that benefit from isolated tracking
- **analysis-agent** - Deep multi-file investigation spanning systems
- **research-agent** - Web searches for implementation examples
- **reference-agent** - Analyzing library source code in reference/ directories

**Use when:** Context efficiency matters. **Skip when:** Simple tasks, quick edits, or overhead isn't worth it.

### 🛠️ Specialized Skills

Skills that auto-activate based on task:

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

### 1. Set Up Memory Bank

```bash
# Create directory structure
mkdir -p memory-bank/{00-core,01-architecture,02-active,03-guides,04-history,05-roadmap}

# Initialize essential files
touch memory-bank/02-active/{currentWork,blockers,nextUp}.md
```

### 2. Set Up Beads

```bash
# Initialize shared/team issues (auto-discovered by directory name)
bd init --prefix myproject

# Optional: Personal tasks namespace
bd init --prefix alex --db .beads/alex/alex.db

# Add to .gitignore
cat >> .gitignore <<EOF
# Ignore database files
.beads/*.db
.beads/*/*.db

# Track shared issues JSONL
!.beads/issues.jsonl
EOF

# Optional: Install git hooks for auto-export
bd hooks install
```

### 3. Start Your First Session

```bash
/prepare
```

Claude will:
- Read Memory Bank files
- Check git status and GitHub account
- List Beads issues ready to work on
- Give you a conversational summary of current state

### 4. Work Through the Workflow

```bash
# Plan your work
/plan Add user authentication

# Execute the plan
/act

# Test your changes (manual review, automated tests, or both)
# Use testing-agent if working with Playwright MCP

# Close the loop
/chart

# Save to branch
git add . && git commit -m "feat: add user authentication"
git push
```

---

## The Circular Workflow

```
┌─────────────────────────────────────────────────────┐
│  /prepare (load context)                            │
│  ↓                                                   │
│  identify work (Beads issue)                        │
│  ↓                                                   │
│  /plan → /elaborate (optional) → /act               │
│  ↓                                                   │
│  review & test (manual + automated)                 │
│  ↓                                                   │
│  /chart (update Beads + Memory Bank)                │
│  ↓                                                   │
│  save to branch (commit + push)                     │
│  ↓                                                   │
│  (next session: /prepare picks up where you left)   │
└─────────────────────────────────────────────────────┘
```

**Key insight:** `/chart` closes the loop by updating tracking systems, ensuring the next session's `/prepare` loads accurate context.

---

## Testing Philosophy

**Test-aware development, not test-driven dogma:**

### When to Lean on Tests

✅ Regression protection (don't break existing features)
✅ Complex logic (auth flows, payment processing, business rules)
✅ Slow feedback loops (deploy → manual check → fix → redeploy)
✅ **Tests tighten the loop**: code → test → fix (seconds, not minutes)

### When to Iterate Directly

✅ Early exploration (don't know what "right" looks like yet)
✅ Visual/UX work (tests can't tell you if it "feels good")
✅ Rapid prototyping (tests would slow velocity)
✅ **Manual review is faster**: change → refresh browser → see it

### The Dance

**Balance between:**
- Manual iteration speed (fast feedback on feel)
- Automated regression protection (prevent breaking things)

**Testing-agent helps with:** Running formalized tests efficiently (isolates Playwright token overhead in separate context).

---

## Configuration

### Memory Bank Structure Options

**Recommended (post-Oct 2025):**
```
memory-bank/
├── 00-core/
├── 01-architecture/
├── 02-active/        ← Start here
├── 03-guides/
├── 04-history/
└── 05-roadmap/
```

**Legacy (flat structure also supported):**
```
memory-bank/
├── projectbrief.md
├── currentWork.md
├── progress.md
└── systemPatterns.md
```

`/prepare` command adapts to whichever structure exists.

### Beads Database Paths

**Standard pattern (recommended):**
- Shared: `.beads/{project}.db` (auto-discovered)
- Shared export: `.beads/issues.jsonl` (git-tracked)
- Personal: `.beads/{name}/{name}.db` (explicit `--db` flag)

**Key principle:** Use defaults for shared/team work, explicit `--db` flag for personal namespace.

---

## Development

### Testing the Plugin Locally

```bash
# Run with debug logging
claude --debug

# Verify plugin loaded
claude plugin list

# Test commands
/prepare
/plan Add a feature
/act
/chart
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
