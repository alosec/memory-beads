# Customizing for Your Team

**Plugin:** memory-beads v0.1.0
**Last Updated:** 2025-11-08

---

## Overview

The memory-beads plugin is designed to be adapted to your team's specific workflow. This guide shows how to customize Memory Bank structure, Beads prefixes, agent behavior, and more.

---

## Memory Bank Customization

### Adjust Folder Structure

The default structure is:
```
memory-bank/
├── shared/          # Team knowledge
└── {name}/          # Personal workspace
```

**Option 1: Per-feature namespaces**
```
memory-bank/
├── shared/
├── auth-team/
├── frontend-team/
└── backend-team/
```

**Option 2: Role-based namespaces**
```
memory-bank/
├── shared/
├── engineering/
├── design/
└── product/
```

Update `.clauderules` to document your structure.

### Custom Active Files

You can add project-specific tracking files to `02-active/`:

```
02-active/
├── currentWork.md       # Standard
├── blockers.md          # Standard
├── nextUp.md            # Standard
├── deployments.md       # Custom: track live deployments
├── incidents.md         # Custom: track active incidents
└── sprint-goals.md      # Custom: current sprint objectives
```

Update `/prepare` command to read your custom files.

---

## Beads Customization

### Project Prefix

Choose meaningful prefixes for your Beads databases:

**Don't:** Generic like `proj`, `team`, `work`
**Do:** Specific like `auth`, `mobile`, `api`

```bash
# For authentication feature team
bd init --prefix auth --db .beads/auth/auth.db

# Issues become: auth-1, auth-2, auth-3...
```

### Multi-Team Setup

For larger projects with multiple teams:

```
.beads/
├── shared/              # Cross-team issues
│   ├── project.db
│   └── issues.jsonl
├── frontend/
│   ├── frontend.db
│   └── issues.jsonl
└── backend/
    ├── backend.db
    └── issues.jsonl
```

Each team maintains their own issues with unique prefixes.

### Custom Issue Types

Beyond bug/feature/task, add project-specific types:

```bash
# Track spike investigations
bd create "Research GraphQL vs REST" -t spike -p 2

# Track documentation tasks
bd create "Document API endpoints" -t docs -p 3

# Track technical debt
bd create "Refactor legacy auth" -t debt -p 1
```

Note: Beads doesn't enforce types, so any string works.

---

## Agent Customization

### Modify Agent Behavior

Edit agent markdown files to adjust behavior:

**Example: Add project-specific patterns to planning-agent**

```bash
# Edit the agent
vi ~/.claude/plugins/memory-beads/agents/planning-agent.md
```

Add a section:

```markdown
## Project-Specific Patterns

When planning for this project:
- Always use TypeScript strict mode
- Follow atomic design for components
- Use Zod for validation schemas
- Prefer server components over client components
```

Agents will follow these guidelines when activated.

### Create Custom Agents

Add new agents for project-specific workflows:

```bash
cd ~/.claude/plugins/memory-beads/agents
```

Create `deployment-agent.md`:

```markdown
---
name: deployment-agent
description: Deploy to production with safety checks and rollback plans. Activate when user mentions deploy to production, release, or go live.
tools: Read, Bash, Grep
model: sonnet
---

# Deployment Agent

You are an expert at safe production deployments.

## Workflow

1. Verify all tests pass
2. Check for breaking changes
3. Create rollback plan
4. Execute deployment
5. Monitor for errors
6. Notify team

...
```

Update `plugin.json` to include it:

```json
"agents": [
  ...
  "./agents/deployment-agent.md"
]
```

---

## Command Customization

### Modify Existing Commands

Example: Customize `/prepare` for your team:

```bash
vi ~/.claude/plugins/memory-beads/commands/prepare.md
```

Add team-specific checks:

```markdown
## Team-Specific Checks

- Check Jira for assigned tickets
- Verify VPN connection
- Check Docker containers running
- Review team calendar for meetings
```

### Add New Commands

Create `/standup` command:

```bash
vi ~/.claude/plugins/memory-beads/commands/standup.md
```

```markdown
# Standup Report

Generate daily standup summary.

## Instructions

1. Read `memory-bank/shared/02-active/currentWork.md`
2. List Beads issues in_progress
3. List Beads issues blocked
4. Format as standup report:
   - Yesterday: [work completed]
   - Today: [planned work]
   - Blockers: [issues]
```

Update `plugin.json`:

```json
"commands": [
  ...
  "./commands/standup.md"
]
```

---

## Skill Customization

### Modify Skill Triggers

Edit skill descriptions to match your team's language:

```bash
vi ~/.claude/plugins/memory-beads/skills/planning/SKILL.md
```

Update description:

```yaml
---
name: Feature Planning
description: Create implementation plans. Activate when user says "let's plan", "design this", "how should we build", "RFC for", or "spec out".
---
```

Now it activates on your team's specific phrases.

### Add Team-Specific Skills

Create a code review skill:

```bash
mkdir ~/.claude/plugins/memory-beads/skills/code-review
vi ~/.claude/plugins/memory-beads/skills/code-review/SKILL.md
```

```yaml
---
name: Code Review
description: Review pull requests for quality, security, and team standards. Activate when user mentions "review PR", "check this code", or "code review".
allowed-tools: Read, Grep, Bash
---

# Code Review Skill

## Checklist

- [ ] Tests added/updated
- [ ] No console.logs or debugger statements
- [ ] TypeScript strict mode compliant
- [ ] Follows atomic design patterns
- [ ] Zod schemas for API validation
- [ ] Error handling implemented
- [ ] Performance considerations addressed

...
```

Update `plugin.json`:

```json
"skills": [
  ...
  "./skills/code-review/SKILL.md"
]
```

---

## Workflow Customization

### Custom Orchestration Patterns

Example: Add design review step:

1. Prepare
2. **Design Review** (new step)
3. Plan
4. Code
5. Test
6. Finalize

Create `design-review-agent.md`:

```markdown
---
name: design-review-agent
description: Review designs for accessibility, responsiveness, and brand compliance before implementation.
---

# Design Review Agent

Before coding UI features:
1. Check Figma designs match requirements
2. Verify accessibility (WCAG 2.1 AA)
3. Confirm responsive breakpoints
4. Validate brand guidelines
5. Suggest component reuse opportunities

...
```

Update orchestrator workflow documentation in your project's `.clauderules`.

---

## Team Adoption Strategies

### Gradual Rollout

**Week 1:** Memory Bank only
- Set up structure
- Create core docs
- Use `/prepare` daily

**Week 2:** Add Beads
- Initialize databases
- Create first issues
- Practice export/import

**Week 3:** Use commands
- Everyone uses `/prepare`
- Try `/plan` for one feature
- Review effectiveness

**Week 4:** Enable agents
- Use planning-agent for all new features
- Try testing-agent for E2E validation
- Evaluate results

### Team Training

Create a quick-start guide for your team:

```markdown
# Quick Start: memory-beads Plugin

## Daily Workflow

1. Start session: `/prepare`
2. Check issues: `bd ready --db .beads/shared/project.db`
3. Plan work: `/plan [feature]`
4. Implement
5. Before push: `bd export --db .beads/shared/project.db -o .beads/shared/issues.jsonl`
6. After pull: `bd import --db .beads/shared/project.db -i .beads/shared/issues.jsonl`

## Memory Bank Rules

- Document discoveries in `shared/03-guides/`
- Keep `02-active/currentWork.md` under 100 lines
- Archive old sessions to `04-history/sessions/YYYY-MM/`

...
```

---

## Integration with Existing Tools

### Connect to Jira/Linear

Create a sync script:

```bash
#!/bin/bash
# scripts/sync-jira-to-beads.sh

# Fetch open Jira tickets
jira list --assignee=me --status=open | while read issue; do
  # Create Beads issue
  bd create "JIRA-$issue" -t task -p 1 --db .beads/shared/project.db
done
```

### Connect to Slack

Post Beads updates to Slack on push:

```bash
# .git/hooks/post-commit

#!/bin/bash
if git diff --name-only HEAD~1 | grep -q ".beads.*issues.jsonl"; then
  # Parse changes and post to Slack
  webhook_url="YOUR_SLACK_WEBHOOK"
  curl -X POST -H 'Content-type: application/json' \
    --data '{"text":"New Beads issues created"}' \
    $webhook_url
fi
```

---

## Performance Tuning

### Reduce Context Usage

For large projects, limit what `/prepare` reads:

```markdown
# In commands/prepare.md

**Optimization:** Only read last 50 lines of currentWork.md if file is large.

```bash
tail -n 50 memory-bank/shared/02-active/currentWork.md
```
```

### Agent Tool Restrictions

Limit agent tools to prevent unnecessary operations:

```yaml
# In agents/planning-agent.md
---
tools: Read, Grep  # Remove Glob and Bash for faster, focused planning
---
```

---

## Metrics and Analytics

### Track Adoption

Add to your project:

```bash
# scripts/plugin-stats.sh

echo "=== memory-beads Usage Stats ==="
echo "Memory Bank docs: $(find memory-bank -name '*.md' | wc -l)"
echo "Beads issues: $(bd list --db .beads/shared/project.db | wc -l)"
echo "Active issues: $(bd list --status open --db .beads/shared/project.db | wc -l)"
echo "Commits with beads: $(git log --oneline | grep -c 'beads:')"
```

### Measure Impact

Before/after metrics:
- Time to orient new developer
- Average PR review time
- Knowledge loss after team member leave
- Issue priority clarity

---

## Related Documentation

- **Architecture:** [../00-core/architecture.md](../00-core/architecture.md)
- **Agent Usage:** [agent-usage.md](./agent-usage.md)
- **Installation:** [installation.md](./installation.md)

---

**The plugin is a starting point. Customize it to match your team's unique workflow!**
