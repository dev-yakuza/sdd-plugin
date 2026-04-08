# AI Review (Independent Agent Review)

Spawn a separate review agent to independently verify the stage output.

## Review Loop:

Execute the following loop (maximum **3 rounds**):

### 1. Self-Review
- Review the output using the self-review criteria defined in the calling command
- If issues found → fix them immediately and continue to step 2
- If no issues → continue to step 2

### 2. AI Review (Independent Agent)
- Use the **Agent tool** to create a review agent with the following prompt:
  - Include the **stage output only** (the content to review)
  - Include the **review criteria** for the current stage (see below)
  - Include the **original Issue body** for reference
  - Do **NOT** include the reasoning process or context that generated the output
  - The agent must review and report: issues found, suggestions, and a pass/fail verdict

### 3. Evaluate Results
- If AI Review **pass** → exit loop, proceed to user review
- If AI Review **fail** → fix the issues, increment round counter, repeat from step 1
- If round counter reaches **3** → exit loop with current state, proceed to user review

## Review Criteria by Stage:
- **analyze**: Are features clearly defined? Is What/Why sufficient? Missing requirements? Ambiguous descriptions? Priorities make sense?
- **design**: Does design match requirements? Missing impact scope? Feasible? Architecture consistent? Risks identified?
- **implement**: Code quality? Test coverage? Pattern consistency? No unnecessary code? PR description accurate?
- **test**: Test coverage sufficient? Edge cases covered? Regression risks addressed?
