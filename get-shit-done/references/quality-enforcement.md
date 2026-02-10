<quality_enforcement>

# Quality Enforcement Reference

Master reference for all quality rules. Referenced by executor, workflows, and reviewers.

## TDD Tiers

### off (default)
No enforcement. Tests written as convenient.

### basic
Prompt-based RED-GREEN-REFACTOR cycle:
1. **RED:** Write failing test first. Commit: `test({phase}-{plan}): ...`
2. **GREEN:** Write minimal code to pass. Commit: `feat({phase}-{plan}): ...`
3. **REFACTOR:** Clean up (tests stay passing). Commit only if changes: `refactor({phase}-{plan}): ...`

### full
Everything in basic, plus hook enforcement:
- **tdd-guard hook** blocks Write/Edit on non-test files when tests fail
- **Read-only test contract:** Gherkin-derived test assertions cannot be modified during GREEN phase
- **Coverage gate:** Must meet `coverage_threshold` after GREEN phase
- **Traceability:** Gherkin IDs in test describe/it blocks

## Read-Only Test Contract (full tier)

When specs are enabled and Gherkin scenarios are derived:
- Test files are generated from Gherkin scenarios
- During GREEN phase, you may NOT modify:
  - Test assertions (expect/assert statements)
  - Scenario descriptions (it/test names)
  - Test preconditions (Given/setup)
- You MAY add:
  - Helper functions
  - Test utilities
  - Setup/teardown
  - Additional test cases beyond Gherkin

## Coverage Gates

After GREEN phase:
```bash
npm run test:coverage
```

Coverage must meet `quality.coverage_threshold` (default: 100%) for files modified in the current task.

If below threshold:
1. Identify uncovered lines
2. Add tests for uncovered paths
3. Re-run coverage
4. Repeat until threshold met

## Full-Stack Test Strategy

When applicable, test at multiple levels:

| Test Level | What | Framework |
|------------|------|-----------|
| Unit | Pure functions, utilities | Vitest |
| Integration | Component + state + API | Vitest + Testing Library |
| E2E | Full user flows | Playwright |

Happy paths → E2E. Edge cases → Integration/Unit. Error cases → Unit.

## Traceability Chain

```
Spec Requirement  →  Gherkin Scenario  →  Test Name  →  Verification  →  Feedback
      TL-3              TL-3.2         "TL-3.2: ..."    "TL-3 step 2"   "TL-3.2 failed"
```

This chain enables:
- Finding which test covers which requirement
- Tracing a failure back to the spec
- Grouping verification by spec capability
- Targeted feedback on specific capabilities

## Two-Stage Review

### Stage 1: Spec Compliance
- Reviewer compares implementation to EARS spec
- Checks: missing requirements, extra work, misunderstandings
- Must pass before Stage 2

### Stage 2: Code Quality
- Reviewer checks code quality independent of spec
- Categories: Critical (must fix), Important (should fix), Minor (nice to fix)
- Critical/Important issues block commit

## Quality Checkpoints

### CP1: Before Implementation (plan-phase)
Required artifacts based on config:
- Always: CONTEXT.md, PLAN.md(s)
- When `quality.specs`: SPEC.md, GHERKIN.md
- When `quality.mockups`: mockup file

Block execution if required artifacts missing.

### CP2: After Implementation (execute-phase)
- Automated: tests, types, lint, build, coverage
- Manual: verification guide grouped by spec sections
- Feedback: issues saved to `.feedback/` if enabled

</quality_enforcement>
