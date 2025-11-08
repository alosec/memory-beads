# Project Brief: Claude Memory + Beads Plugin

**Created:** 2025-11-08
**Status:** Active Development
**Version:** 0.1.0

---

## What This Is

A **complete workflow plugin for Claude Code** that packages the proven Memory Bank + Beads system for team collaboration. This plugin provides orchestrated subagent workflows, productivity commands, and specialized skills that transform how development teams work with Claude.

---

## The Problem

**Development teams using Claude Code face:**
- **Context scattered** across code, docs, issues, and conversations
- **No clear "start here"** when beginning work sessions
- **Discoveries lost** when knowledge isn't systematically captured
- **Priorities unclear** without explicit dependency tracking
- **Inconsistent workflows** across team members

**Individual developers struggle with:**
- Re-reading massive codebases to orient each session
- Forgetting what was decided in previous conversations
- No systematic way to graduate exploration → validated knowledge
- Manual coordination of planning, coding, and testing phases

---

## The Solution

This plugin provides **three integrated systems:**

### 1. Memory Bank
Structured documentation with shared/personal namespaces:
- **Fast orientation:** Read 3 files (~250 lines) vs 10k+ lines of code
- **Knowledge graduation:** Explore in personal space → validate → share with team
- **Self-cleaning:** Archive old sessions, keep active docs lean
- **Queryable history:** Dated sessions, topic-based organization

### 2. Beads Issue Tracking
Git-native issue tracker with dependency awareness:
- **Version-controlled issues:** JSONL format alongside code
- **Dependency tracking:** Explicit blockers and relationships
- **Simple workflow:** export before push, import after pull
- **Queryable priorities:** `bd ready` shows unblocked work instantly

### 3. Orchestrated Subagents
Specialized agents working in isolated contexts:
- **planning-agent:** Analyzes requirements → creates implementation plans
- **coding-agent:** Executes plans → writes code following patterns
- **testing-agent:** Deploys branches → runs E2E tests with visual validation
- **Main orchestrator:** Coordinates workflow, handles git operations

---

## Who This Is For

**Teams adopting Claude Code** who want:
- Consistent workflow patterns across developers
- Systematic knowledge capture and sharing
- Dependency-aware issue tracking in git
- Automated orchestration of plan → code → test cycles

**Individual developers** who want:
- Fast session orientation (3 files, 250 lines, 3-5 minutes)
- Systematic documentation of discoveries
- Productivity commands (/prepare, /plan, /navigate)
- Filtered build/deploy output to reduce context noise

---

## What Makes This Different

**Compared to traditional issue trackers:**
- ✅ Lives in git (no external service)
- ✅ Human-readable JSONL format
- ✅ Dependency-aware by design
- ✅ Works offline, syncs via git

**Compared to flat documentation:**
- ✅ Structured namespaces (shared vs personal)
- ✅ Knowledge graduation workflow
- ✅ Self-cleaning (archive old, keep current lean)
- ✅ Fast orientation (prioritized reading order)

**Compared to ad-hoc AI workflows:**
- ✅ Orchestrated subagents (isolated contexts)
- ✅ Systematic handoffs (planning → coding → testing)
- ✅ Reusable patterns across all features
- ✅ Built-in productivity commands

---

## Success Metrics

**For teams:**
- Time to orient new developer: < 30 minutes (read memory-bank, run /prepare)
- Context switches: ~3 minutes (read currentWork, blockers, nextUp)
- Knowledge loss: Minimal (explorations archived, patterns documented)
- Coordination overhead: Reduced (dependencies explicit, priorities queryable)

**For individuals:**
- Session startup: ~5 minutes (/prepare reads 250 lines)
- Feature planning: ~10 minutes (/plan creates structure + tasks)
- Testing workflow: Automated (testing-agent handles deploy + E2E + visual validation)

---

## Roadmap

**v0.1.0 (Current):**
- ✅ 6 orchestrated subagents
- ✅ 5 productivity commands
- ✅ 4 specialized skills
- ✅ Complete documentation and examples
- ✅ Memory Bank structure deployed
- ✅ Beads database initialized with sample issues

**v0.2.0 (Next):**
- [ ] CI/CD integration examples (GitHub Actions, GitLab CI)
- [ ] Pre-commit hooks for auto-export
- [ ] Enhanced /prepare with issue recommendations
- [ ] Team adoption metrics and analytics

**v1.0.0 (Future):**
- [ ] Plugin marketplace listing (if public)
- [ ] Video tutorials and workshops
- [ ] Integration templates for popular stacks
- [ ] Multi-project workspace support

---

## Related Documentation

- **Architecture:** [architecture.md](./architecture.md) - How the plugin is structured
- **Installation:** [../01-guides/installation.md](../01-guides/installation.md) - Setup and configuration
- **Agent Usage:** [../01-guides/agent-usage.md](../01-guides/agent-usage.md) - Working with subagents
- **Current Work:** [../02-active/currentWork.md](../02-active/currentWork.md) - Development status

---

**This plugin represents a complete workflow system, battle-tested and ready for team adoption.**
