# Claude Dev Tools

Lean dependency analysis and large file tools for Claude Code. Designed to work alongside memory plugins like [claude-mem](https://github.com/thedotmack/claude-mem).

## Features

- **Dependency Analysis** - Query imports, find circular dependencies, impact analysis
- **Progressive Reader** - Read large files efficiently (75-97% token savings)
- **Large File Guard** - Automatically blocks reads on files >50KB
- **TOON Format** - Token-optimized dependency graphs (~40% fewer tokens than JSON)

## Installation

### From Source (Recommended)

```bash
git clone https://github.com/soderalohastrom/claude-dev-tools.git
cd your-project
bash /path/to/claude-dev-tools/install
```

### Remote Install

```bash
curl -fsSL https://raw.githubusercontent.com/soderalohastrom/claude-dev-tools/main/install | bash
```

### Double-Click Install (macOS)

Copy `install-claude-dev-tools.command` to your project root and double-click.

## Requirements

- **Go 1.20+** - For building dependency-scanner and progressive-reader
- **Bash 4.0+** - For shell tools
- **Python 3** - For path resolution in toon-parser

## Usage

### Slash Commands (Recommended)

After installation, use these in Claude Code:

```
/deps src/auth.ts           # What imports this file?
/impact src/auth.ts         # What breaks if I change this?
/circular                   # Find circular dependencies
/deadcode                   # Find unused files
/deps-tree src/auth.ts      # Visualize dependency tree
/large-file src/big.ts      # Read large file progressively
/scan-deps                  # Rebuild dependency graph
/verify-tools               # Verify installation
```

### Shell Commands (Alternative)

```bash
# What imports this file?
bash .claude/tools/query-deps/query-deps.sh src/auth.ts

# What would break if I change this file?
bash .claude/tools/impact-analysis/impact-analysis.sh src/auth.ts

# Find circular dependencies
bash .claude/tools/find-circular/find-circular.sh

# Find unused files
bash .claude/tools/find-dead-code/find-dead-code.sh

# Visualize dependency tree
bash .claude/scripts/show-deps-tree.sh src/auth.ts
```

### Progressive Reader (Large Files)

```bash
# See file structure (functions, classes, chunks)
.claude/bin/progressive-reader --path src/big-file.ts --list

# Read specific chunk
.claude/bin/progressive-reader --path src/big-file.ts --chunk 3
```

### Rebuild Dependency Graph

Run after pulling new code or when imports change:

```bash
.claude/bin/dependency-scanner --path . --output .claude/dep-graph.toon
```

## How It Works

1. **dependency-scanner** (Go) parses your codebase and builds a dependency graph in `.toon` format
2. **Shell tools** query the graph for imports, impact analysis, circular deps
3. **progressive-reader** (Go) uses tree-sitter to chunk large files intelligently
4. **large-file-guard** hook intercepts Read tool calls and blocks files >50KB

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

## Supported Languages

- TypeScript / JavaScript (full AST parsing)
- Python (full AST parsing)
- Go (full AST parsing)
- Other languages (line-based chunking fallback)

## License

MIT
