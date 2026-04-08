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
- If `$0` is "help" or empty → read `${CLAUDE_SKILL_DIR}/commands/help.md` and display
- If `$0` is anything else → report unknown command, then read `${CLAUDE_SKILL_DIR}/commands/help.md` and display

---

## Common Definitions

### Stage Flow
```
1. analyze (What/Why) → 2. design (How) → 3. implement (TDD) → 4. test (E2E/QA) → done
```

### Labels
`sdd:analyze` → `sdd:design` → `sdd:implement` → `sdd:test` → `sdd:done` | `sdd:child` (child Issue)

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
Before posting a stage output, search Issue comments for the matching marker. If found → update that comment. If not → create new comment.

