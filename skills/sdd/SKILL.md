---
name: sdd
description: "Spec-Driven Development - AI collaborative development process with GitHub integration. Use when working on GitHub Issues with a structured process: analyze → design → implement (TDD) → test."
argument-hint: "<command> [issue-number]"
user-invocable: true
---

# SDD (Spec-Driven Development)

You are executing the SDD process. Route to the appropriate command based on `$0`.

## Command Routing

- If `$0` is "init" → execute **INIT** with language `$1` (default: en)
- If `$0` is "analyze" → execute **ANALYZE** with issue `$1`
- If `$0` is "design" → execute **DESIGN** with issue `$1`
- If `$0` is "implement" → execute **IMPLEMENT** with issue `$1`
- If `$0` is "test" → execute **TEST** with issue `$1`
- If `$0` is "status" → execute **STATUS** with issue `$1`
- If `$0` is "review" → execute **REVIEW** with issue `$1`
- If `$0` is "help" or empty → execute **HELP**

---

## INIT

Set up SDD for the current GitHub repository.

**Language argument:** `$1` determines the language for Issue templates.
- `ko`, `korean`, `한국어` → Korean
- `ja`, `japanese`, `日本語` → Japanese
- `en`, `english`, or empty → English (default)

1. Copy Issue templates from `${CLAUDE_SKILL_DIR}/templates/{lang}/issue_*.yml` to `.github/ISSUE_TEMPLATE/`
   - Select the template directory based on the language argument
2. Create GitHub labels:
   ```bash
   gh label create "sdd:analyze" --color "1d76db" --description "SDD: Requirements Analysis" --force
   gh label create "sdd:design" --color "0e8a16" --description "SDD: Design" --force
   gh label create "sdd:implement" --color "e4e669" --description "SDD: Implementation" --force
   gh label create "sdd:test" --color "f9d0c4" --description "SDD: Testing" --force
   gh label create "sdd:done" --color "0075ca" --description "SDD: Done" --force
   ```
3. Report completion with a summary of what was installed.

---

## ANALYZE

**Stage 1: Requirements Analysis (What / Why)**

Focus ONLY on What and Why. Do NOT discuss How (technical implementation).

### Process:
1. Read the Issue: `gh issue view $1`
2. Classify the request type (new feature / enhancement / bug fix / refactoring)
3. Analyze What (feature list) and Why (background, motivation)
4. If information is missing, ask the user (do NOT ask How questions)
5. Split into feature list with priorities
6. Format output using the template in `${CLAUDE_SKILL_DIR}/templates/output_analyze.md`

### Self-Review:
- Check for missing features, ambiguous descriptions
- Verify What/Why are sufficient
- If issues found, go back and improve

### User Review:
- Present the output to the user
- Ask for confirmation on direction and priorities
- On approval: post as Issue comment and update label to `sdd:design`

```bash
# Post output as Issue comment
gh issue comment $1 --body "$(cat <<'EOF'
<output content here>
EOF
)"

# Update label
gh issue edit $1 --remove-label "sdd:analyze" --add-label "sdd:design"
```

---

## DESIGN

**Stage 2: Design (How)**

Define HOW to implement based on the requirements.

### Process:
1. Read analyze output from Issue comments:
   ```bash
   gh api repos/{owner}/{repo}/issues/$1/comments --jq '.[].body' | grep -A 1000 'sdd:analyze:output' | grep -B 1000 '/sdd:analyze:output'
   ```
2. Explore the codebase — analyze existing architecture and patterns
3. Identify impact scope (related files, screens, data)
4. Design file structure changes
5. Design data model changes (if applicable)
6. Identify constraints and risks
7. Create feature list with PR split
8. Format output using the template in `${CLAUDE_SKILL_DIR}/templates/output_design.md`

### Self-Review:
- Verify design matches requirements
- Check for missing impact scope
- Assess feasibility

### User Review:
- Present the output to the user
- Ask for confirmation on technical approach and PR split
- On approval: post as Issue comment and update label to `sdd:implement`

---

## IMPLEMENT

**Stage 3: Implementation - TDD Cycle (Red → Green → Refactor)**

Execute per PR, repeating the TDD cycle.

Test scope: Unit tests / UI tests (widget tests, golden tests, etc.)

### For each PR:

#### 3-0. PR Kickoff: Plan
1. Read design output from Issue comments
2. Create feature branch (`feat/feature-name`)
3. Write test plan for this PR
4. Write implementation plan based on test plan
5. **Self-review**: verify test coverage and feasibility
6. **User review**: confirm plan direction before proceeding

#### 3-1. Red: Write Failing Tests
1. Write test code based on test plan
2. Run tests → confirm failure (Red)
3. **Self-review**: check test code quality
4. **User review**: confirm test code

#### 3-2. Green: Minimal Implementation
1. Implement minimal code to pass tests
2. Run tests → confirm pass (Green)
3. **Self-review**: ensure no unnecessary code
4. **User review**: confirm implementation direction

#### 3-3. Refactor: Improve Code
1. Remove duplication, improve readability, clean up structure
2. Run tests → confirm still passing (Green)
3. **Self-review**: check code quality and pattern consistency
4. **User review**: confirm refactoring result

#### 3-4. PR Creation & Code Review
1. Summarize changes
2. Create PR: `gh pr create --title "..." --body "..."`
3. Re-run all tests → confirm pass
4. **Self-review**: check code consistency, verify no missing features
5. **User review**: final confirmation
6. If more PRs remain → go back to 3-0
7. All PRs done → update label to `sdd:test`

---

## TEST

**Stage 4: Testing**

Unit/UI tests are already done in Stage 3. This stage focuses on E2E and QA.

### Process:
1. Write E2E test code (integration tests)
2. Start test environment → run E2E tests → check results
3. Create QA checklist based on requirements
4. Identify regression test targets

### User Tasks:
1. Manual QA testing based on checklist
2. Run regression tests

### Self-Review:
- Analyze test logs, estimate bug causes
- If E2E tests fail, fix test code or identify bugs
- If bugs found → go back to Stage 3 for TDD bug fix cycle

### User Review:
- Confirm all test results
- All tests pass → update label to `sdd:done`

```bash
gh issue edit $1 --remove-label "sdd:test" --add-label "sdd:done"
```

---

## STATUS

Check the current progress of an Issue.

1. Read Issue labels: `gh issue view $1 --json labels`
2. Check Issue comments for each stage output:
   - `<!-- sdd:analyze:output -->` exists?
   - `<!-- sdd:design:output -->` exists?
3. Check related PRs: `gh pr list --search "issue:$1"`
4. Summarize current stage and progress

Output format:
```
Issue #$1: <title>
Stage: <current stage based on label>
- [x] Analyze: completed
- [x] Design: completed
- [ ] Implement: PR #1 in progress
- [ ] Test: not started
```

---

## REVIEW

Review the current stage output of an Issue.

1. Determine current stage from Issue labels
2. Read the latest output from Issue comments
3. Perform AI review based on stage:
   - **analyze**: missing features, ambiguous descriptions, What/Why sufficient?
   - **design**: design matches requirements? missing impact scope? feasible?
   - **implement**: code quality, pattern consistency, test coverage?
   - **test**: test coverage, regression risks?
4. Post review result as Issue comment

---

## HELP

Display available commands and usage:

```
SDD (Spec-Driven Development) - AI Collaborative Development Process

Commands:
  /sdd init              Set up SDD for the current repository (Issue templates, labels)
  /sdd analyze <issue>   Stage 1: Requirements Analysis (What/Why)
  /sdd design <issue>    Stage 2: Design (How)
  /sdd implement <issue> Stage 3: Implementation with TDD (Red → Green → Refactor)
  /sdd test <issue>      Stage 4: E2E and QA Testing
  /sdd status <issue>    Check current progress
  /sdd review <issue>    AI review of current stage output
  /sdd help              Show this help message

Workflow:
  1. Create an Issue using SDD templates
  2. /sdd analyze <issue>   → analyzes What/Why, posts output to Issue
  3. /sdd design <issue>    → designs How, posts output to Issue
  4. /sdd implement <issue> → TDD cycle per PR
  5. /sdd test <issue>      → E2E tests and QA
```
