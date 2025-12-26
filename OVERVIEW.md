# Claude Dev Tools - Overview

Lean dependency analysis and large file tools for Claude Code.

## What This Is

A lightweight toolkit providing:
- **Dependency Analysis** - Query imports, impact analysis, circular deps, dead code detection
- **Progressive Reader** - Efficiently read large files (>50KB) without token overflow
- **Large File Guard** - Hook that prevents accidental large file reads

Designed to work **alongside** memory plugins like [claude-mem](https://github.com/thedotmack/claude-mem).

---

## Quick Start

### Option 1: Double-Click Install (Recommended)

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
│   │   └── show-deps-tree.sh
│   ├── hooks/
│   │   └── large-file-guard.sh
│   ├── settings.local.json       # Hook configuration
│   └── dep-graph.toon            # Dependency graph (generated)
└── CLAUDE.md                     # Instructions for Claude
```

---

## Available Commands

Once installed, use these in your Claude Code sessions:

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

## Rebuilding the Dependency Graph

Run this when:
- After cloning a new repo
- After pulling new code
- When imports change significantly

```bash
.claude/bin/dependency-scanner --path . --output .claude/dep-graph.toon
```

---

## Requirements

- **Go 1.20+** - For building the Go binaries
- **Bash 4.0+** - For shell tools
- **Python 3** - Optional, for path resolution

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

Does NOT include:
- Memory/capsule systems (use claude-mem instead)
- Session sync (use claude-mem instead)
- Discovery logging (unnecessary overhead)

---

## License

MIT
