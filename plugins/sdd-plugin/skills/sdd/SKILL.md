---
name: sdd
description: "Spec-Driven Development - AI collaborative development process with GitHub integration. Use when working on GitHub Issues with a structured process: analyze → design → implement (TDD) → test."
argument-hint: "<command> [issue-number]"
user-invocable: true
---

# SDD (Spec-Driven Development)

You are executing the SDD process. Route to the appropriate command based on `$0`.

**IMPORTANT:** After routing, read the command detail file from `${CLAUDE_SKILL_DIR}/commands/<command>.md` and execute the instructions in it.

## Command Routing

- If `$0` is "init" → read `${CLAUDE_SKILL_DIR}/commands/init.md` and execute with language `$1` (default: en)
- If `$0` is "analyze" → read `${CLAUDE_SKILL_DIR}/commands/analyze.md` and execute with issue `$1`
- If `$0` is "design" → read `${CLAUDE_SKILL_DIR}/commands/design.md` and execute with issue `$1`
- If `$0` is "implement" → read `${CLAUDE_SKILL_DIR}/commands/implement.md` and execute with issue `$1`
- If `$0` is "test" → read `${CLAUDE_SKILL_DIR}/commands/test.md` and execute with issue `$1`
- If `$0` is "resume" → read `${CLAUDE_SKILL_DIR}/commands/resume.md` and execute with issue `$1`
- If `$0` is "status" → read `${CLAUDE_SKILL_DIR}/commands/status.md` and execute with issue `$1`
- If `$0` is "review" → read `${CLAUDE_SKILL_DIR}/commands/review.md` and execute with issue `$1`
- If `$0` is "rollback" → read `${CLAUDE_SKILL_DIR}/commands/rollback.md` and execute with issue `$1` and target stage `$2`
- If `$0` is "help" or empty → execute **HELP** below
- If `$0` is anything else → report unknown command and execute **HELP** below

---

## Common Definitions

### Stage Flow
```
1. analyze (What/Why) → 2. design (How) → 3. implement (TDD) → 4. test (E2E/QA) → done
```

### Labels
| Label | Stage | Color |
|-------|-------|-------|
| `sdd:analyze` | Requirements Analysis | `#1d76db` |
| `sdd:design` | Design | `#0e8a16` |
| `sdd:implement` | Implementation | `#e4e669` |
| `sdd:test` | Testing | `#f9d0c4` |
| `sdd:done` | Complete | `#0075ca` |
| `sdd:child` | Child Issue | `#d4c5f9` |

### Output Markers
| Marker | Location | Purpose |
|--------|----------|---------|
| `<!-- sdd:analyze:output -->` | Issue comment | Analysis result |
| `<!-- sdd:design:output -->` | Issue comment | Design result |
| `<!-- sdd:children:output -->` | Issue comment | Child Issue list (parent only) |
| `<!-- sdd:child-issue -->` | Issue body | Child Issue identifier |
| `<!-- sdd:rollback -->` | Issue comment | Rollback notice |

### Parent/Child Issue Detection
- **Parent Issue**: has `<!-- sdd:children:output -->` marker in comments
- **Child Issue**: body contains `Parent Issue: #<number>` inside `<!-- sdd:child-issue -->` block
- **Single Issue**: neither parent nor child

### Language Setting
- Stored in `.github/.sdd-lang` (default: en)
- Set by `/sdd init [lang]`
- Languages: `en` (default), `ko`/`korean`/`한국어`, `ja`/`japanese`/`日本語`

### Duplicate Output Prevention
When posting stage outputs (analyze/design), check if a previous output exists first:
```bash
EXISTING_COMMENT_ID=$(gh api repos/{owner}/{repo}/issues/$1/comments \
  --jq '.[] | select(.body | contains("<marker>")) | .id' | tail -1)
```
- If exists → update the existing comment instead of creating a duplicate
- If not → post new output as Issue comment

---

## HELP

Display available commands and usage:

```
SDD (Spec-Driven Development) - AI Collaborative Development Process

Commands:
  /sdd init [lang]       Set up SDD for the current repository (Issue templates, labels)
                         Languages: en (default), ko/korean/한국어, ja/japanese/日本語
  /sdd analyze <issue>   Stage 1: Requirements Analysis (What/Why)
  /sdd design <issue>    Stage 2: Design (How)
  /sdd implement <issue> Stage 3: Implementation with TDD (Red → Green → Refactor)
  /sdd test <issue>      Stage 4: E2E and QA Testing
  /sdd resume <issue>    Auto-detect current stage and continue from where it left off
  /sdd rollback <issue> <stage>  Roll back to a previous stage (analyze, design, implement)
  /sdd status <issue>    Check current progress
  /sdd review <issue>    AI review of current stage output
  /sdd help              Show this help message

Workflow:
  1. /sdd init [lang]       → set up repository (choose language for templates)
  2. Create an Issue using SDD templates
  3. /sdd analyze <issue>   → analyzes What/Why, posts output to Issue
  4. /sdd design <issue>    → designs How, posts output to Issue
  5. /sdd implement <issue> → TDD cycle per PR
  6. /sdd test <issue>      → E2E tests and QA

  Interrupted? Run: /sdd resume <issue> → auto-detects stage and continues
```
