# Claude Dev Tools

Lean dependency analysis and large file tools for Claude Code.

## Quick Reference

| Task | Command |
|------|---------|
| What imports this file? | `bash .claude/tools/query-deps/query-deps.sh <file>` |
| What breaks if I change X? | `bash .claude/tools/impact-analysis/impact-analysis.sh <file>` |
| Find circular dependencies | `bash .claude/tools/find-circular/find-circular.sh` |
| Find unused files | `bash .claude/tools/find-dead-code/find-dead-code.sh` |
| Visualize dependency tree | `bash .claude/scripts/show-deps-tree.sh <file>` |

## Large Files (>50KB)

The PreToolUse hook blocks Read on files over 50KB. Use progressive-reader instead:

```bash
# See file structure (~500 tokens vs full read)
.claude/bin/progressive-reader --path <file> --list

# Read specific chunk
.claude/bin/progressive-reader --path <file> --chunk N
```

## Rebuild Dependency Graph

Run when imports change or after pulling new code:

```bash
.claude/bin/dependency-scanner --path . --output .claude/dep-graph.toon
```

## Compatibility

Works alongside other Claude Code plugins (e.g., claude-mem for memory).
