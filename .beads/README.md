# Beads Issue Tracking for Plugin Development

This directory demonstrates the Beads git-native issue tracking system in action.

## Structure

```
.beads/
├── .gitignore          # Ignores *.db files
├── README.md           # This file
└── plugin/
    ├── plugin.db       # Local database (not tracked)
    └── issues.jsonl    # Git-tracked issues (source of truth)
```

## Quick Start

### Initialize (already done for this plugin)

```bash
cd ~/code/ideaflow/claude-memory-beads
bd init --prefix plugin --db .beads/plugin/plugin.db
```

### Working with Issues

```bash
# Create issue
bd create "Add new command" -t feature -p 1 --db .beads/plugin/plugin.db

# List issues
bd list --db .beads/plugin/plugin.db

# Show details
bd show plugin-1 --db .beads/plugin/plugin.db

# Update status
bd update plugin-1 --status in_progress --db .beads/plugin/plugin.db

# Close issue
bd close plugin-1 -r "Implemented in commit abc123" --db .beads/plugin/plugin.db
```

### Sync with Git

**Before pushing:**
```bash
bd export --db .beads/plugin/plugin.db -o .beads/plugin/issues.jsonl
git add .beads/plugin/issues.jsonl
git commit -m "beads: update plugin-1 status"
git push
```

**After pulling:**
```bash
git pull
bd import --db .beads/plugin/plugin.db -i .beads/plugin/issues.jsonl
```

## Use This as Reference

This `.beads/` directory serves as a working example for teams adopting the plugin. See how issues are tracked, exported, and version-controlled alongside code.

For complete workflow guide, see: `memory-bank/01-guides/beads-workflow.md`
