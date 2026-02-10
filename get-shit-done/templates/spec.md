---
phase: {PHASE_NUMBER}-{PHASE_NAME}
type: spec
format: ears
---

# Feature: {PHASE_NAME}

> Context: {from CONTEXT.md — relevant user decisions and phase boundary}

**Goal:** {single sentence — why this phase exists, from user perspective}

**Scope**
- In: {included capabilities}
- Out: {explicitly excluded — reference deferred items from CONTEXT.md}

**Dependencies** (if applicable)
- Requires: {must exist from prior phases}
- Triggers: {side effects on other systems}

---

## {ID}-1: {Capability Name}

- WHEN {trigger} THE SYSTEM SHALL {response}
- {additional EARS criteria}

**Examples:**
| Input | Result |
|-------|--------|
| {concrete data from CONTEXT.md or user} | {concrete outcome} |

---

## {ID}-2: {Capability Name}

- WHEN {trigger} THE SYSTEM SHALL {response}
- WHILE {state} THE SYSTEM SHALL {behavior}

**Examples:**
| Before | Action | After |
|--------|--------|-------|
| {state} | {action} | {new state} |

---

## Rules (include only sections that apply)

**Business Validation**
- BV1: {rule}

**Input Validation**
- IV1: {constraint}

---

## Data Model

**T1: {table}**
- {field} ({client-friendly type})

---

## Out of Scope

- {explicitly excluded with reasoning}
