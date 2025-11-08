# Working with Subagents

**Plugin:** memory-beads v0.1.0
**Last Updated:** 2025-11-08

---

## Overview

The memory-beads plugin includes 6 specialized subagents that work together in an orchestrated workflow. Each agent operates in its own isolated context window with focused expertise.

**The Pattern:**
```
Main Claude (Orchestrator)
    ↓ delegates
Planning Agent → Creates plan
    ↓ returns plan
Coding Agent → Implements code
    ↓ returns summary
Testing Agent → Runs E2E tests
    ↓ returns results
Main Claude → Commits, pushes, creates PR
```

---

## The Six Agents

### 1. planning-agent

**Purpose:** Analyze requirements and create implementation plans

**When it activates:**
- User runs `/plan` command
- User asks to "plan a feature", "design implementation", "create a plan"
- Main orchestrator delegates planning work

**What it does:**
1. Quick code review (2-3 minutes) to understand patterns
2. Analyze the feature request for core requirements
3. Create clear understanding summary (2-3 sentences)
4. Generate file structure tree with [C]/[M]/[D] annotations
5. Provide numbered execution tasks
6. Optionally create Beads issues for complex features

**Example usage:**
```
User: Plan an authentication system for the app

planning-agent responds with:
- Understanding: "I'll build a user authentication system..."
- File tree showing new/modified files
- Execution tasks: 1. Create AuthContext, 2. Add login form, etc.
```

**Returns to orchestrator:** Concise plan summary

---

### 2. coding-agent

**Purpose:** Execute implementation plans step-by-step

**When it activates:**
- Main orchestrator delegates implementation work
- User explicitly requests "implement this plan"

**What it does:**
1. Receives plan from planning-agent
2. Uses TodoWrite to track progress through tasks
3. Implements code following established patterns
4. Marks todos as in_progress/completed as it works
5. Follows project style guides and conventions

**Example workflow:**
```
Orchestrator: "Implement this authentication plan: [plan details]"

coding-agent:
- Marks "Create AuthContext" as in_progress
- Writes the code
- Marks as completed
- Moves to next task
- Returns: "Implemented auth system. Files changed: AuthContext.tsx, LoginForm.tsx, api/auth.ts"
```

**Returns to orchestrator:** Files changed and key decisions made

---

### 3. testing-agent

**Purpose:** Deploy feature branches and run E2E tests with visual validation

**When it activates:**
- User mentions "test", "e2e", "deploy feature branch", "test deployment"
- Main orchestrator delegates testing work
- User explicitly requests testing workflow

**What it does:**
1. Deploys feature branch to get live URL
2. Runs Playwright E2E tests against deployment
3. Captures screenshots at mobile (375×667), tablet (768×1024), desktop (1280×720)
4. Tails deployment logs for debugging
5. Analyzes failures and provides actionable feedback
6. Emails visual report showing complete user journey

**Example workflow:**
```
Orchestrator: "Test the authentication feature on deployment"

testing-agent:
- Deploys branch to Vercel/Cloudflare
- Gets preview URL
- Runs E2E tests
- Captures screenshots of login flow
- Returns: "Tests passed. Email sent with 12 screenshots across 3 breakpoints."
```

**Returns to orchestrator:** Test results, failure analysis, visual evidence

---

### 4. analysis-agent

**Purpose:** Deep investigation of complex codebase issues

**When it activates:**
- User asks "why is this happening"
- Issues span multiple systems or files
- Need to trace data flow through layers

**What it does:**
1. Multi-file investigation of intricate issues
2. Traces data flow through system layers
3. Identifies complex interactions and dependencies
4. Provides root cause analysis

**Example usage:**
```
User: Why is the cache growing unbounded?

analysis-agent:
- Reads UpdateManager, CacheService, Node lifecycle
- Traces cache write/invalidation flow
- Identifies missing cleanup on node deletion
- Returns: "Root cause: cache invalidation missing in deleteNode(). Fix needed in UpdateManager.ts:142"
```

**Returns to orchestrator:** Root cause analysis and fix recommendations

---

### 5. research-agent

**Purpose:** Search web for implementation examples and patterns

**When it activates:**
- User asks "how do others implement X"
- Need reference implementations
- Looking for best practices or patterns

**What it does:**
1. Searches web for real-world examples
2. Finds implementation patterns for AI SDK, tool calling, streaming
3. Validates approaches against community knowledge
4. Summarizes best practices

**Example usage:**
```
User: How do others implement streaming with AI SDK?

research-agent:
- Searches for "AI SDK streaming implementation examples"
- Finds reference implementations
- Returns: "Common pattern: use streamText() with onChunk callback. Examples from Vercel docs and community repos..."
```

**Returns to orchestrator:** Research findings and reference links

---

### 6. reference-agent

**Purpose:** Analyze library source code to understand APIs

**When it activates:**
- Need to understand how a library actually works internally
- Debug framework-specific issues
- Validate correct usage patterns against source

**What it does:**
1. Reads library source code in reference/ directory
2. Analyzes actual implementation vs documentation
3. Debugs unexpected API behavior
4. Validates usage patterns

**Example usage:**
```
User: Why is assistant-ui's useChat behaving unexpectedly?

reference-agent:
- Reads reference/assistant-ui source
- Traces useChat implementation
- Returns: "useChat expects onFinish callback to be stable. Your inline function recreates on each render, causing re-initialization."
```

**Returns to orchestrator:** Source-level understanding and corrections

---

## Orchestrated Workflow Example

**Complete feature development cycle:**

```
1. User: "Add dark mode toggle to settings"

2. Main Claude: Runs /prepare
   - Reads memory-bank (currentWork, blockers, nextUp)
   - Checks git status
   - Reviews Beads issues

3. Main Claude: Delegates to planning-agent
   planning-agent →
   - Analyzes existing theme patterns
   - Creates file structure tree
   - Generates execution tasks
   - Returns plan

4. Main Claude: Delegates to coding-agent
   coding-agent →
   - Implements ThemeContext
   - Creates DarkModeToggle component
   - Integrates into SettingsPanel
   - Returns: "Files changed: ThemeContext.tsx, DarkModeToggle.tsx, SettingsPanel.tsx"

5. Main Claude: Delegates to testing-agent
   testing-agent →
   - Deploys feature branch
   - Runs E2E tests
   - Captures screenshots
   - Returns: "Tests passed. Visual report emailed."

6. Main Claude: Finalizes
   - Creates commit: "feat: add dark mode toggle to settings"
   - Pushes to remote
   - Creates PR
   - Updates memory-bank
```

**Total context used:**
- Main thread: Clean, only summaries
- planning-agent context: Isolated planning details
- coding-agent context: Isolated implementation details
- testing-agent context: Isolated test output

**Efficiency gain:** ~70% reduction in main context usage compared to doing everything in one thread

---

## Best Practices

### 1. Use the Right Agent

**Don't:** Ask main Claude to plan, code, and test all in one go
**Do:** Let the orchestrator delegate to appropriate agents

**Don't:** Manually invoke agents directly
**Do:** Use commands (/plan) or let auto-activation work

### 2. Trust the Handoffs

Agents return concise summaries to the orchestrator. The orchestrator has all the context it needs.

**Don't:** Ask agents to explain every detail back
**Do:** Review the summary and move forward

### 3. Leverage Isolation

Each agent's context stays clean and focused.

**Don't:** Worry about polluting context with planning details
**Do:** Let planning-agent explore deeply - it won't affect coding-agent

### 4. Follow the Pattern

The orchestrated workflow is:
1. Prepare (orient)
2. Plan (analyze + design)
3. Code (implement)
4. Test (validate)
5. Finalize (commit + PR)

**Don't:** Skip steps or combine phases
**Do:** Let each agent complete its phase fully

---

## Advanced Usage

### Custom Agent Invocation

You can explicitly request an agent in conversation:

```
User: Use the analysis-agent to investigate why the cache is growing

Main Claude will delegate to analysis-agent even if description matching wouldn't auto-activate it.
```

### Parallel Agent Usage

The orchestrator can invoke multiple agents in parallel when tasks are independent:

```
User: Research best practices AND analyze current implementation

Main Claude might invoke:
- research-agent (searches web in parallel)
- analysis-agent (investigates code in parallel)

Both return results simultaneously.
```

### Agent Tool Restrictions

Some agents have restricted tools for focused workflows:

- **planning-agent:** Read, Grep, Glob, Bash (no Write/Edit - read-only exploration)
- **coding-agent:** All tools (full implementation capability)
- **testing-agent:** All tools + shell management (for log tailing)

This prevents scope creep and keeps agents focused.

---

## Debugging Agents

### Agent Not Activating

**Check description matching:**

```bash
# View agent description
head -n 10 ~/.claude/plugins/memory-beads/agents/planning-agent.md
```

Make sure your request includes trigger words from the description.

### Agent Returning Incomplete Results

Agents are designed to return summaries, not full details. If you need more:

```
User: Show me the complete implementation plan

Main Claude will ask the agent to expand its response.
```

### Agent Making Wrong Decisions

Provide more context upfront:

**Don't:** "Plan a feature"
**Do:** "Plan a feature for dark mode. We use CSS variables for theming already."

More context → better plans

---

## Related Documentation

- **Architecture:** [../00-core/architecture.md](../00-core/architecture.md) - How agents fit in the system
- **Installation:** [installation.md](./installation.md) - Setting up the plugin
- **Customization:** [customization.md](./customization.md) - Adapting workflows

---

**The orchestrated agent workflow is the core value proposition of this plugin. Use it!**
