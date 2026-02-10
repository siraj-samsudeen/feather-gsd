# Quality Enforcement Integration Plan

This document captures the full integration plan for bringing Feather's quality enforcement capabilities into the GSD framework. It serves as a reference for future sessions and PR work.

## Status

All 10 phases implemented on `main` branch. PR work in progress.

### Upstream PR Strategy

Three focused PRs from fresh branches off `upstream/main`:

| PR | Branch | Scope | Status |
|----|--------|-------|--------|
| 1 | `feat/quality-config-tdd` | Config extension + Three-tier TDD | Branch ready, PR pending |
| 2 | (future) | Structured feedback system | Not started |
| 3 | (future) | Spec traceability chain | Not started |

### PR-worthy vs Fork-only

**PR-worthy (upstream):**
- Config extension with quality section (non-breaking, additive)
- Three-tier TDD in executor (extends existing `tdd="true"`)
- Structured feedback system (objectively better than prose UAT.md)
- Quality checkpoints (configurable gates)
- Traceability chain concept (spec ID in task XML)

**Fork-only (opinionated):**
- EARS spec format, 100% coverage default, full-stack-only test strategy
- HTML mockups via Playwright, design exploration, two-stage review

---

## Design Decisions

### D1: Artifacts live in GSD's `.planning/phases/` structure
`{phase}-SPEC.md`, `{phase}-GHERKIN.md` sit alongside `CONTEXT.md` and `RESEARCH.md`.

### D2: `.feedback/` lives at project root
Spans phases, persists across project lifecycle.

### D3: Config is additive — all quality features default to off
No existing config migration needed. `loadConfig()` uses defaults for missing values.

### D4: TDD is a three-tier option
- `off` — no enforcement (GSD default)
- `basic` — prompt-based RED-GREEN-REFACTOR cycle
- `full` — hook-enforced with coverage gates and read-only test contract

### D5: Gherkin review is always a gate
Scenarios presented for user approval before implementation.

### D6: PR-worthy vs fork-only separation
See table above.

---

## Implementation Phases (All Complete)

### Phase 1: Config Foundation
Added `quality` section to config.json, `loadConfig()` in gsd-tools.js, planning-config.md docs.

### Phase 2: Settings & Setup Commands
`/gsd:setup-quality` (presets: minimal/standard/full), `/gsd:setup-tdd-hooks`, settings.md integration, new-project.md quality round.

### Phase 3: EARS Specs + Design Exploration
EARS syntax reference, spec template, `/gsd:create-spec`, discuss-phase integration.

### Phase 4: Gherkin Derivation + Traceability
`gsd-test-deriver` agent, Gherkin template, `/gsd:derive-tests`, plan-phase auto-derivation.

### Phase 5: Spec-Aware Planning
Planner task XML with `spec_ref`, Gherkin IDs in verify blocks.

### Phase 6: TDD Enforcement Tiers
Three-tier TDD in executor (off/basic/full), quality-enforcement.md reference.

### Phase 7: Two-Stage Review
`gsd-spec-reviewer` and `gsd-code-reviewer` agents, reviewer prompts.

### Phase 8: Structured Feedback + Polish Queue
`.feedback/` system, `/gsd:feedback`, `/gsd:polish`, verify-work integration.

### Phase 9: UI Mockups
`/gsd:mockup`, text diagram → HTML → Playwright workflow.

### Phase 10: Quality Checkpoints
CP1 (pre-implementation artifact gate), CP2 (post-implementation quality checks).

---

## Files Summary

### Modified (14 existing files)
1. `get-shit-done/templates/config.json`
2. `get-shit-done/bin/gsd-tools.js`
3. `get-shit-done/references/planning-config.md`
4. `agents/gsd-executor.md`
5. `agents/gsd-planner.md`
6. `get-shit-done/workflows/discuss-phase.md`
7. `get-shit-done/workflows/plan-phase.md`
8. `get-shit-done/workflows/execute-phase.md`
9. `get-shit-done/workflows/execute-plan.md`
10. `get-shit-done/workflows/verify-work.md`
11. `get-shit-done/workflows/settings.md`
12. `get-shit-done/workflows/new-project.md`
13. `get-shit-done/references/tdd.md`
14. `CLAUDE.md`

### New (25 files)
**Agents:** gsd-spec-reviewer, gsd-code-reviewer, gsd-test-deriver
**Commands:** setup-quality, setup-tdd-hooks, create-spec, derive-tests, mockup, feedback, polish
**Workflows:** setup-quality, setup-tdd-hooks, create-spec, derive-tests, create-mockup, feedback, polish
**Templates:** spec, gherkin, feedback-item, feedback-index
**References:** quality-enforcement, ears-spec-format, spec-reviewer-prompt, code-quality-reviewer-prompt

---

## For Future Sessions

To build PR 2 (Structured Feedback):
```
git fetch upstream
git checkout -b feat/structured-feedback upstream/main
# Surgically reconstruct feedback system from main branch reference
# Key files: templates/feedback-*.md, commands/gsd/feedback.md, workflows/feedback.md, verify-work.md changes
```

To build PR 3 (Spec Traceability):
```
git fetch upstream
git checkout -b feat/spec-traceability upstream/main
# Key files: agents/gsd-test-deriver.md, templates/spec.md, templates/gherkin.md, commands/gsd/create-spec.md, derive-tests.md, planner spec_ref changes
```
