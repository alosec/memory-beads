# Plugin Architecture

**Last Updated:** 2025-11-08
**Version:** 0.1.0

---

## Overview

The memory-beads plugin is structured as a Claude Code plugin with five component types: agents, commands, skills, hooks (future), and MCP servers (future). This document explains how each component works and how they integrate together.

---

## Directory Structure

```
memory-beads/
├── .claude-plugin/
│   └── plugin.json              # Manifest defining all entry points
├── .beads/                      # Example Beads setup
│   ├── .gitignore
│   ├── README.md
│   └── plugin/
│       ├── plugin.db            # Local database (not tracked)
│       └── issues.jsonl         # Git-tracked issues
├── memory-bank/                 # Example Memory Bank structure
│   ├── README.md
│   ├── 00-core/                 # Foundation docs
│   ├── 01-guides/               # How-to documentation
│   └── 02-active/               # Current work tracking
├── agents/                      # 6 specialized subagents
│   ├── planning-agent.md        # Plan features
│   ├── coding-agent.md          # Implement code
│   ├── testing-agent.md         # E2E testing
│   ├── analysis-agent.md        # Deep analysis
│   ├── research-agent.md        # Web research
│   └── reference-agent.md       # Library analysis
├── commands/                    # 5 slash commands
│   ├── prepare.md               # Workspace orientation
│   ├── plan.md                  # Feature planning
│   ├── navigate.md              # Codebase navigation
│   ├── define-skill.md          # Skill creation
│   └── new-window.md            # Window management
├── skills/                      # 4 auto-activating skills
│   ├── planning/SKILL.md        # Planning workflow
│   ├── testing/SKILL.md         # Testing workflow
│   ├── quiet-build/SKILL.md     # Filtered build
│   └── quiet-deploy/SKILL.md    # Filtered deploy
├── README.md                    # User-facing documentation
├── marketplace.json             # Distribution config
└── LICENSE                      # MIT license
```

---

## Component Types

### 1. Agents (Subagents)

**Location:** `agents/*.md`

**Purpose:** Specialized AI assistants that operate in separate context windows with focused expertise.

**Structure:**
```markdown
---
name: agent-name
description: When and why to use this agent
tools: Read, Grep, Glob  # Optional - inherits all if omitted
model: sonnet            # Optional - sonnet, opus, haiku, or inherit
---

# Agent System Prompt

Detailed instructions for how this agent should behave...
```

**Key Agents:**

1. **planning-agent** (464 lines)
   - Analyzes requirements and existing code patterns
   - Creates implementation plans with file structure trees
   - Generates numbered execution tasks
   - Creates Beads issues for complex features

2. **coding-agent** (118 lines)
   - Executes implementation plans step-by-step
   - Follows established code patterns
   - Uses TodoWrite to track progress
   - Returns concise implementation summary

3. **testing-agent** (539 lines)
   - Deploys feature branches to get live URLs
   - Runs Playwright E2E tests against deployments
   - Captures screenshots at multiple breakpoints
   - Emails visual reports of user journeys
   - Debugs failures with log tailing

4. **analysis-agent** (149 lines)
   - Deep investigation of complex codebase issues
   - Traces data flow through multiple layers
   - Identifies intricate system interactions

5. **research-agent** (99 lines)
   - Searches web for implementation examples
   - Finds real-world patterns and best practices
   - Validates approaches against community knowledge

6. **reference-agent** (46 lines)
   - Analyzes library source code in reference/ directories
   - Validates correct API usage
   - Debugs framework-specific issues

**How They Work:**
- Main Claude (orchestrator) delegates tasks to specialized agents
- Each agent operates in its own isolated context window
- Agents return concise summaries to the orchestrator
- Clean handoffs prevent context pollution

---

### 2. Commands (Slash Commands)

**Location:** `commands/*.md`

**Purpose:** User-invoked workflows triggered by `/command-name` syntax.

**Structure:**
```markdown
# Command Name

**Task**: $ARGUMENTS

## Instructions

Detailed workflow for what this command should do...
```

**Key Commands:**

1. **/prepare** (78 lines)
   - Reads Memory Bank (currentWork, blockers, nextUp)
   - Checks git status and recent commits
   - Runs `gh auth status` to verify GitHub account
   - Checks Beads issues with `bd ready`
   - Provides conversational workspace summary

2. **/plan** (56 lines)
   - Analyzes user's feature request
   - Reviews relevant codebase patterns
   - Creates file structure tree ([C]/[M]/[D] annotations)
   - Generates numbered execution tasks
   - Activates planning-agent or planning skill

3. **/navigate** (71 lines)
   - Helps explore codebase structure
   - Finds files and patterns quickly
   - Provides oriented code tour

4. **/define-skill** (135 lines)
   - Interactive skill creation wizard
   - Guides through SKILL.md structure
   - Creates proper YAML frontmatter
   - Sets up skill directory with templates

5. **/new-window** (123 lines)
   - Manages Claude Code windows
   - Opens new sessions with context
   - Handles multi-window workflows

---

### 3. Skills

**Location:** `skills/*/SKILL.md`

**Purpose:** Auto-activating capabilities that load into current context based on description matching.

**Structure:**
```markdown
---
name: Skill Name
description: When to use this and specific trigger terms
allowed-tools: Bash, Read, Edit  # Optional tool restrictions
---

# Skill Instructions

Workflow and best practices...
```

**Key Skills:**

1. **planning** (147 lines)
   - Auto-activates when user asks to plan, analyze, or design
   - Quick code review (2-3 minutes)
   - File structure planning with ASCII trees
   - Execution task breakdown

2. **testing** (376 lines)
   - Activates when user mentions testing, e2e, or deployment
   - Complete workflow: deploy → test → capture → email → iterate
   - Multi-breakpoint screenshot capture
   - Visual validation across user journey

3. **quiet-build** (42 lines)
   - Filters verbose build output
   - Shows only errors and key status
   - Reduces context usage during CI

4. **quiet-deploy** (52 lines)
   - Filters verbose deploy output
   - Highlights critical deployment steps
   - Reduces context usage for Cloudflare/Vercel deploys

**Skill vs Agent:**
- **Skills:** Load into current conversation, single-purpose, composable
- **Agents:** Separate context window, complex workflows, isolated

---

## Orchestration Pattern

The plugin enables a powerful orchestrated workflow:

```
User Request
     ↓
Main Claude (Orchestrator)
     ├─→ /prepare → Orient to workspace
     ├─→ /plan → Delegate to planning-agent
     │           Returns: Implementation plan
     ├─→ Delegate implementation to coding-agent
     │           Returns: Files changed summary
     ├─→ Delegate testing to testing-agent
     │           Returns: Test results
     └─→ Create commit, push, PR
```

**Benefits:**
- **Context efficiency:** Each agent's details stay in its context
- **Clean handoffs:** Agents return concise summaries
- **Reusable patterns:** Same workflow for all features
- **Natural mapping:** Mirrors real development stages

---

## Configuration Files

### plugin.json

**Location:** `.claude-plugin/plugin.json`

**Purpose:** Manifest defining all plugin entry points

**Required Fields:**
- `name` - Plugin identifier (kebab-case)

**Recommended Fields:**
- `version` - Semantic versioning
- `description` - What the plugin does
- `author` - Name and URL
- `keywords` - Searchable terms
- `agents` - Array of `./agents/*.md` paths
- `commands` - Array of `./commands/*.md` paths
- `skills` - Array of `./skills/*/SKILL.md` paths

### marketplace.json

**Location:** `marketplace.json` (plugin root)

**Purpose:** Distribution configuration for plugin marketplaces

**Key Fields:**
- `displayName` - Human-friendly name
- `repository` - Git URL for source
- `visibility` - "public" or "private"
- `installation` - How to install the plugin
- `features` - Marketing descriptions of capabilities

---

## Design Principles

### 1. Context Efficiency

**Problem:** Claude Code has token limits per conversation

**Solution:**
- Subagents isolate complex workflows in separate contexts
- Skills provide focused capabilities without full agent overhead
- Filtered output (quiet-build, quiet-deploy) reduces noise

### 2. Progressive Disclosure

**Problem:** Users don't need all documentation upfront

**Solution:**
- Memory Bank has prioritized reading order (02-active → 00-core → 01-guides)
- Commands provide just-in-time workflows (/prepare before each session)
- Skills auto-activate only when relevant

### 3. Systematic Knowledge Capture

**Problem:** Discoveries get lost in chat history

**Solution:**
- Memory Bank with shared/personal namespaces
- Knowledge graduation (explore → validate → share)
- Dated session archives in 04-history/

### 4. Dependency Awareness

**Problem:** Can't tell what's blocked or ready to work on

**Solution:**
- Beads tracks explicit blockers
- `bd ready` shows unblocked work
- Git-native tracking alongside code

---

## Extension Points

### Future Additions

1. **Hooks**
   - Pre-commit: Auto-export Beads
   - Post-pull: Auto-import Beads
   - Pre-push: Validate memory-bank structure

2. **MCP Servers**
   - Beads integration for live issue queries
   - Memory Bank search across team docs
   - Project analytics and insights

3. **Additional Commands**
   - `/checkpoint` - Save conversation state
   - `/reflect` - Analyze session progress
   - `/export-session` - Archive to memory-bank

4. **Additional Skills**
   - **code-review** - Automated PR review workflow
   - **refactor** - Safe refactoring patterns
   - **migrate** - Database/API migration workflows

---

## Performance Considerations

**Agent Context Windows:**
- Each subagent gets its own token budget
- Orchestrator stays lean (only receives summaries)
- Parallel agent invocation where possible

**Skill Activation:**
- Description matching is keyword-based
- Multiple skills can activate simultaneously
- Tool restrictions prevent capability conflicts

**File Organization:**
- Agents separated from commands (different invocation)
- Skills in subdirectories (support files like templates)
- Examples (memory-bank/, .beads/) demonstrate usage

---

## Related Documentation

- **Project Brief:** [projectBrief.md](./projectBrief.md) - What and why
- **Installation:** [../01-guides/installation.md](../01-guides/installation.md) - Setup
- **Agent Usage:** [../01-guides/agent-usage.md](../01-guides/agent-usage.md) - Workflows
- **Current Work:** [../02-active/currentWork.md](../02-active/currentWork.md) - Status

---

**This architecture enables scalable, maintainable workflow automation for development teams.**
