# Capy Cortex - Autonomous Learning System

You have a persistent learning brain powered by SQLite + FTS5 + sklearn TF-IDF.
Knowledge is automatically loaded via hooks. This file describes manual operations.

## Architecture

- **Database**: `~/.claude/skills/capy-cortex/cortex.db` (SQLite + FTS5 + WAL)
- **Hooks** (automatic, never call manually):
  - SessionStart: Loads anti-patterns, preferences, principles
  - UserPromptSubmit: Retrieves task-relevant rules via FTS5
  - PreToolUse(Bash): Blocks known dangerous commands
  - PostToolUseFailure: Records errors as anti-patterns
  - Stop: Extracts corrections and preferences from conversation
- **Scripts** (for manual/scheduled use):
  - `cortex.py`: Core engine (retrieve, add rules, stats)
  - `reflect.py`: Deep session analysis
  - `consolidate.py`: Cluster rules into principles (sklearn)
  - `bootstrap.py`: Mine historical sessions

## Manual Commands

# Check system health

# Retrieve rules for a topic

# Add a rule manually

# Add an anti-pattern

# Add a preference

# Run consolidation (clusters rules into principles)

# Retrain TF-IDF model

# Apply confidence decay
```

## How It Learns

1. **Automatic** (via hooks): Errors are captured, corrections noted, preferences extracted
2. **Reflection**: Deep analysis of session transcripts extracts patterns
3. **Consolidation**: sklearn clustering groups similar rules into principles
4. **Decay**: Old, unreinforced rules fade; validated rules strengthen
5. **Retrieval**: Two-stage FTS5 + TF-IDF returns only relevant knowledge (O(1) context)