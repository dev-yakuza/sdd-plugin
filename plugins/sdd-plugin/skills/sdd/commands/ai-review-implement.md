# AI Review Criteria: implement

## Previous stage outputs to include:
- design output

## Review Points:

Each review point in the implement stage has its own review type and checklist.
Use the section matching the current review point.

---

### Plan (3-0)

**Review type: self_only**

#### Self-Review Checklist:
- [ ] Test plan covers all design items for this PR scope
- [ ] Test plan classifies cases by behavioral path (happy, error, boundary, concurrent/state)
- [ ] Implementation plan is consistent with the test plan
- [ ] Plan follows the architecture decisions from the design

#### Additional Self-Review:
Beyond the checklist above, check for any other issues from your own perspective regarding feasibility, approach, or risk. Compare against design output: does the plan address all planned changes for this PR?

---

### Red - Test Code (3-1)

**Review type: self_only**

#### Self-Review Checklist:
- [ ] Tests cover the main scenarios from the test plan
- [ ] Tests cover edge cases identified in the design
- [ ] Test assertions are specific and meaningful
- [ ] Tests actually fail (Red state confirmed)

#### Additional Self-Review:
Beyond the checklist above, check for any other issues from your own perspective regarding test quality, readability, or missing scenarios.

---

### Green - Implementation (3-2)

**Review type: self_only**

#### Self-Review Checklist:
- [ ] Implementation is minimal — only enough to pass the tests
- [ ] Code follows existing codebase patterns and conventions
- [ ] All tests pass (Green state confirmed)

#### Additional Self-Review:
Beyond the checklist above, check for any other issues from your own perspective regarding code correctness, readability, or potential side effects.

---

### Refactor (3-3)

**Review type: self_only**

#### Self-Review Checklist:
- [ ] Duplication is removed and readability is improved
- [ ] No unnecessary code, comments, or debug artifacts remain
- [ ] All tests still pass (Green state confirmed)

#### Additional Self-Review:
Beyond the checklist above, check for any other issues from your own perspective regarding code structure, naming, or maintainability.

---

### E2E Test (3-4)

**Review type: self_only**

#### Self-Review Checklist:
- [ ] E2E tests cover the key user flows for this feature
- [ ] E2E tests follow existing test framework patterns and directory structure
- [ ] E2E tests pass successfully

#### Additional Self-Review:
Beyond the checklist above, check for any other issues from your own perspective regarding test reliability, flakiness risk, or missing user flows.

---

### PR Final (3-5)

**Review type: full**

#### Required Checklist:
- [ ] All design items for this PR scope are implemented
- [ ] Tests cover the main scenarios and edge cases from the design
- [ ] Code follows existing codebase patterns and conventions
- [ ] No unnecessary code, comments, or debug artifacts remain
- [ ] PR description accurately reflects the changes
- [ ] Manual test checklist covers UI behavior and edge cases not in automated tests

#### Cross-stage Check:
- Compare against design output: are the planned changes fully implemented as designed?

#### Additional Review:
Beyond the checklist above, report any issues found from your own perspective regarding code quality, performance, or security.
