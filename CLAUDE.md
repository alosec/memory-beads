# Memory Bank + Beads Workflow Plugin

This repository provides a complete workflow system for Claude Code featuring Memory Bank documentation patterns and Beads issue tracking integration.

---

## Project Configuration

**Repository:** https://github.com/alosec/memory-beads
**Type:** Claude Code plugin
**Beads Prefix:** `bd-` (for issues in this repo)

---

## Memory Bank Format Standards

This plugin implements the Oct 2025 Memory Bank structure. When using `/chart` to update Memory Bank files, follow these conventions:

### Core Active Files (02-active/)

#### currentWork.md Format

```markdown
# Current Work

**Updated:** YYYY-MM-DD

## This Week's Focus

[1-2 sentence summary of primary objective]

## Active Tasks

### [Task Name] (Status: 🔄 IN PROGRESS / ⏸️ PAUSED / ✅ COMPLETE)
- **Started:** YYYY-MM-DD
- **Beads Issue:** project-XXX
- **Status:** [Current state in plain English]
- **Next Step:** [Immediate next action]
- **Files:**
  - path/to/file.ts
  - path/to/other.ts

## Recently Completed

### [Completed Task] (Completed: YYYY-MM-DD)
- **Beads Issue:** project-XXX (closed)
- **Outcome:** [What was shipped]
- **Learnings:** [Key insights]
- **Commit:** abc1234

## Context Notes

[Any important context for understanding current work]
```

**Guidelines:**
- Keep under ~100 lines total
- Archive completed tasks to `04-history/sessions/` weekly
- Update at session start and session end
- Link to Beads issues with IDs

#### blockers.md Format

```markdown
# Blockers & Impediments

**Updated:** YYYY-MM-DD

## Active Blockers

### 🚨 [Blocker Name] (P0/P1/P2)
- **Discovered:** YYYY-MM-DD
- **Impact:** [What's blocked and why it matters]
- **Beads Issue:** project-XXX
- **Status:** [Current investigation/workaround status]
- **Next Action:** [Who needs to do what]

## Watching

### ⚠️ [Potential Issue]
- **Discovered:** YYYY-MM-DD
- **Description:** [What we're monitoring]
- **If it gets worse:** [Escalation plan]

## Recently Resolved

### ✅ [Resolved Blocker] (Resolved: YYYY-MM-DD)
- **Resolution:** [How it was fixed]
- **Beads Issue:** project-XXX (closed)
```

**Guidelines:**
- Keep under ~50 lines
- P0 = Critical (blocks revenue/launch)
- P1 = High (blocks current sprint)
- P2 = Medium (slows progress)
- Move resolved blockers to "Recently Resolved" before archiving

#### nextUp.md Format

```markdown
# Next Up

**Updated:** YYYY-MM-DD

## Prioritized Work Queue

### P0 - Critical (Do First)
1. [Task] - **Beads:** project-XXX - [Why urgent]
2. [Task] - **Beads:** project-XXX - [Why urgent]

### P1 - High Priority (This Week)
3. [Task] - **Beads:** project-XXX - [Context]
4. [Task] - **Beads:** project-XXX - [Context]

### P2 - Medium Priority (This Sprint)
5. [Task] - **Beads:** project-XXX - [Context]
6. [Task] - **Beads:** project-XXX - [Context]

### P3 - Low Priority (Nice to Have)
7. [Task] - **Beads:** project-XXX - [Context]
8. [Task] - **Beads:** project-XXX - [Context]

### P4 - Ideas / Future
- [Task] - [Brief description]
- [Task] - [Brief description]

## Deferred

- [Task] - **Beads:** project-XXX (blocked by: reason)
- [Task] - **Beads:** project-XXX (waiting on: external dependency)
```

**Guidelines:**
- Keep top 10 tasks prioritized and current
- Link to Beads issues for trackable work
- Move stale items to P4 or archive to roadmap
- Update after completing work or discovering new priorities

### Session Archive Format (04-history/sessions/)

**File naming:** `YYYY-MM-DD-topic-name.md`

```markdown
# Session: [Topic] (YYYY-MM-DD)

**Status:** ✅ COMPLETE / 🔄 IN PROGRESS

## What We Shipped

- [Feature/fix description with user impact]
- [Feature/fix description with user impact]

## Files Changed

- `path/to/file.ts` - [What changed]
- `path/to/other.tsx` - [What changed]

## Beads Issues

- project-XXX: [Issue title] (closed)
- project-YYY: [Issue title] (created/updated)

## Commits

- abc1234 - feat: add dark mode toggle
- def5678 - fix: session cookie handling

## Learnings

- [Key insight or pattern discovered]
- [Technical decision and rationale]
- [Gotcha or edge case to remember]

## Related Docs

- See `01-architecture/systemPatterns.md` for new auth pattern
- See `03-guides/deployment.md` for deployment updates
```

---

## Using /chart Command

The `/chart` command comprehensively updates both Beads and Memory Bank. Common scenarios:

### Bug Discovered

```bash
/chart discovered critical bug in session handling
```

**Expected actions:**
1. Create P0/P1 Beads issue with bug details
2. Add to `blockers.md` if critical
3. Update `currentWork.md` status if blocking work
4. Provide summary of updates made

### Feature Completed

```bash
/chart completed dark mode feature
```

**Expected actions:**
1. Close Beads issue with completion summary (`bd close <id> -r "..."`)
2. Add to "Recently Completed" in `currentWork.md`
3. Move blocker to "Resolved" if applicable
4. Update `nextUp.md` priorities
5. Consider archiving to `04-history/sessions/` if significant

### Initiative Started

```bash
/chart starting user authentication initiative
```

**Expected actions:**
1. Create parent Beads issue for initiative
2. Create child issues for specific tasks
3. Update `currentWork.md` with new focus
4. Add tasks to `nextUp.md` with priority

### Status Update

```bash
/chart progress update on API refactor
```

**Expected actions:**
1. Update relevant Beads issues
2. Refresh `currentWork.md` status
3. Update `blockers.md` if impediments changed
4. Provide summary of current state

---

## Beads Integration

### Project Configuration

**Auto-discovery:** `.beads/bd.db` (for this repo's issues)
**Export:** `.beads/issues.jsonl` (git-tracked)
**Prefix:** `bd-XXX`

### Quick Commands

```bash
# Create issue
bd create "Document Memory Bank format" -t task -p 2

# Update status
bd update bd-123 --status in_progress

# Close issue
bd close bd-456 -r "Added comprehensive Memory Bank documentation"

# List issues
bd ready                    # What's ready to work on
bd list                     # All issues
bd show bd-123             # Specific issue
```

### Git Hooks

If git hooks are installed (`bd hooks install`), commits will auto-export to `.beads/issues.jsonl`.

After `git pull`, run `bd import` to sync local database.

---

## Workflow Commands

### /prepare

Loads context from:
- Memory Bank (02-active files)
- Git status and recent commits
- GitHub account check
- Beads issues (`bd ready`)

### /plan

Creates implementation plan:
- Analyzes codebase patterns
- File structure tree [C]/[M]/[D]
- Numbered execution tasks

### /elaborate

Provides file-level details:
- Specific line changes needed
- Exact modifications per file

### /act

Executes the plan:
- Creates TodoWrite list
- Implements systematically
- Marks progress

### /chart

Updates tracking systems:
- CRUD Beads issues
- Update Memory Bank files
- Cross-reference between systems
- Provide summary

---

## Memory Bank Maintenance Principles

### Keep It Lean

- `currentWork.md` stays under ~100 lines
- Archive completed work to `04-history/sessions/` weekly
- Move outdated tasks from `nextUp.md` to roadmap
- Resolve blockers or move to watching status

### Keep It Accurate

- Update `currentWork.md` at session start and end
- Document blockers as soon as identified
- Archive sessions within a week of completion
- Link Beads issues and Memory Bank files

### Keep It Accessible

- Use clear section headers
- Include Beads issue IDs for cross-reference
- Maintain consistent date format (YYYY-MM-DD)
- Update "Updated:" timestamps when changing files

---

## Remember

**The Memory Bank is Claude's only link to previous work.**

The `/chart` command maintains this link by:
1. Creating/updating/closing Beads issues (trackable tasks)
2. Updating Memory Bank files (context and narrative)
3. Cross-referencing between systems (nothing falls through cracks)
4. Ensuring next session's `/prepare` loads clean, accurate context

**Goal:** 80% context in <10 tool calls via `/prepare`, informed work via documented patterns, closed loop via `/chart`.
