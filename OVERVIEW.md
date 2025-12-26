# Claude Dev Tools - Overview

Lean dependency analysis and large file tools for Claude Code.

## What This Is

A lightweight toolkit providing:
- **Dependency Analysis** - Query imports, impact analysis, circular deps, dead code detection
- **Progressive Reader** - Efficiently read large files (>50KB) without token overflow
- **Large File Guard** - Hook that prevents accidental large file reads
- **Slash Commands** - Quick access to all tools via `/deps`, `/impact`, etc.

Designed to work **alongside** memory plugins like [claude-mem](https://github.com/thedotmack/claude-mem).

---

## Quick Start

### Option 1: Double-Click Install (macOS)

1. Copy `install-claude-dev-tools.command` to your project root
2. Double-click the file
3. Done! Tools are installed to `.claude/`

### Option 2: Clone & Install

```bash
# Clone this repo somewhere
git clone https://github.com/soderalohastrom/claude-dev-tools.git ~/tools/claude-dev-tools

# Go to your project
cd your-project

# Run install
bash ~/tools/claude-dev-tools/install
```

### Option 3: Remote Install (curl)

```bash
cd your-project
curl -fsSL https://raw.githubusercontent.com/soderalohastrom/claude-dev-tools/main/install | bash
```

---

## What Gets Installed

After installation, your project will have:

```
your-project/
├── .claude/
│   ├── bin/
│   │   ├── dependency-scanner    # Go binary
│   │   └── progressive-reader    # Go binary
│   ├── tools/
│   │   ├── query-deps/
│   │   ├── impact-analysis/
│   │   ├── find-circular/
│   │   └── find-dead-code/
│   ├── lib/
│   │   └── toon-parser.sh
│   ├── scripts/
│   │   ├── show-deps-tree.sh
│   │   └── verify-install.sh
│   ├── hooks/
│   │   └── large-file-guard.sh
│   ├── commands/
│   │   ├── deps.md
│   │   ├── impact.md
│   │   ├── circular.md
│   │   ├── deadcode.md
│   │   ├── deps-tree.md
│   │   ├── large-file.md
│   │   ├── scan-deps.md
│   │   └── verify-tools.md
│   ├── settings.local.json       # Hook configuration
│   └── dep-graph.toon            # Dependency graph (generated)
└── CLAUDE.md                     # Instructions for Claude
```

---

## Slash Commands (Recommended)

Use these in your Claude Code sessions:

| Command | Description |
|---------|-------------|
| `/deps <file>` | What imports this file? |
| `/impact <file>` | What breaks if I change this? |
| `/circular` | Find circular dependencies |
| `/deadcode` | Find unused files |
| `/deps-tree <file>` | Visualize dependency tree |
| `/large-file <file>` | Read large files progressively |
| `/scan-deps` | Rebuild dependency graph |
| `/verify-tools` | Verify installation |

---

## Shell Commands (Alternative)

| Task | Command |
|------|---------|
| What imports this file? | `bash .claude/tools/query-deps/query-deps.sh <file>` |
| What breaks if I change X? | `bash .claude/tools/impact-analysis/impact-analysis.sh <file>` |
| Find circular dependencies | `bash .claude/tools/find-circular/find-circular.sh` |
| Find unused files | `bash .claude/tools/find-dead-code/find-dead-code.sh` |
| Visualize dependency tree | `bash .claude/scripts/show-deps-tree.sh <file>` |
| Read large file structure | `.claude/bin/progressive-reader --path <file> --list` |
| Read specific chunk | `.claude/bin/progressive-reader --path <file> --chunk N` |

---

## About TOON Format

The dependency graph uses [TOON (Token-Oriented Object Notation)](https://github.com/toon-format/toon) - a compact data format designed for LLM inputs.

**Why TOON?**
- ~40% fewer tokens than equivalent JSON
- Human-readable with clear structural markers
- Combines YAML-style indentation with CSV-style tables
- Lossless bidirectional conversion with JSON

Example `.toon` structure:
```
META:version=1.0,scanned=2024-12-25
FILE:/src/auth.ts
LANG:typescript
IMPORTS:/src/utils.ts,/src/types.ts
EXPORTS:login,logout,getCurrentUser
IMPORTEDBY:/src/api.ts,/src/app.ts
---
FILE:/src/utils.ts
...
```

---

## Rebuilding the Dependency Graph

Run this when:
- After cloning a new repo
- After pulling new code
- When imports change significantly

```bash
.claude/bin/dependency-scanner --path . --output .claude/dep-graph.toon
```

Or use the slash command: `/scan-deps`

---

## Requirements

- **Go 1.20+** - For building the Go binaries
- **Bash 4.0+** - For shell tools
- **Python 3** - For path resolution in toon-parser

---

## How It Works

1. **dependency-scanner** (Go) parses your codebase and builds a `.toon` graph file
2. **Shell tools** query the graph for imports, circular deps, impact analysis
3. **progressive-reader** (Go) uses tree-sitter to intelligently chunk large files
4. **large-file-guard** hook intercepts Read calls and blocks files >50KB

---

## Supported Languages

Full AST parsing:
- TypeScript / JavaScript
- Python
- Go

Fallback (line-based chunking):
- All other languages

---

## Compatibility

Works with:
- **claude-mem** - For memory and session tracking
- **Other Claude Code plugins** - No conflicts

---

## License

MIT
