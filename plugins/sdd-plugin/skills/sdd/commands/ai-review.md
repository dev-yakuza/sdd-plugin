# AI Review (Independent Agent Review)

Spawn a separate review agent to independently verify the stage output.

## Review Types:

- **full**: Two independent agents in parallel (Completeness + Quality)
- **self_only**: Self-Review only, skip independent agent review

## Review Loop:

Execute the following loop (maximum **3 rounds**):

### 1. Self-Review
- Review the output using the checklist and criteria from the stage-specific review file
- If issues found → fix them immediately and continue to step 2
- If no issues → continue to step 2
- If review type is **self_only** → exit loop after Self-Review, proceed to user review

### 2. AI Review (Two Independent Agents in parallel)

Only executed when review type is **full**.

Launch **two review agents in parallel** using two Agent tool calls in a single message.

Both agents receive the same base context:
- Include the **stage output only** (the content to review)
- Include the **original Issue body** for reference
- Include the **previous stage outputs** for cross-stage consistency check (as defined in the stage-specific review file)
- Do **NOT** include the reasoning process or context that generated the output

#### Agent A: Completeness Reviewer
- Focus: **requirements coverage and cross-stage consistency**
- Use the **Required Checklist** and **Cross-stage Check** from the stage-specific review file
- Report: checklist results (pass/fail per item), consistency issues, each with severity

#### Agent B: Quality Reviewer
- Focus: **quality, risks, and issues beyond the checklist**
- Use the **Additional Review** criteria from the stage-specific review file
- Look for risks, edge cases, ambiguities, pattern violations, or any other concerns
- Report: issues found, suggestions, each with severity

#### Report Format:
Each agent reports a list of issues with severity (do NOT include a verdict — verdict is determined by the combined rules below):
- **critical**: Must fix — incorrect logic, missing requirement, security risk
- **major**: Should fix — inconsistency, poor coverage, unclear specification
- **minor**: Nice to fix — style, naming, minor improvement suggestions

#### Combined Verdict:
Merge issues from both agents, then apply:
- Any **critical** or **major** issue → overall fail
- Only **minor** issues → overall pass (include suggestions for reference)
- No issues → overall pass

### 3. Evaluate Results
- If AI Review **pass** → exit loop, proceed to user review
- If AI Review **fail** → fix the issues, increment round counter, repeat from step 1
  - Record found issues in a review history list
  - In subsequent rounds, include the review history in the agent prompt:
    "Previous round issues: [list]. Verify these are fixed and no new issues were introduced during the fix."
- If round counter reaches **3** → exit loop, report remaining unfixed issues (with severity) to the user, then proceed to user review

## Stage-specific Review Criteria:

Read the review criteria from the corresponding file:
- **analyze**: `${CLAUDE_SKILL_DIR}/commands/ai-review-analyze.md`
- **design**: `${CLAUDE_SKILL_DIR}/commands/ai-review-design.md`
- **implement**: `${CLAUDE_SKILL_DIR}/commands/ai-review-implement.md`
- **test**: `${CLAUDE_SKILL_DIR}/commands/ai-review-test.md`
