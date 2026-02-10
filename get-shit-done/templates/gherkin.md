---
phase: {PHASE_NUMBER}-{PHASE_NAME}
type: gherkin
derived_from: {phase}-SPEC.md
scenario_count: {N}
---

# Gherkin Scenarios: Phase {PHASE_NUMBER} — {PHASE_NAME}

Derived from `{phase}-SPEC.md`. {N} scenarios across {M} capability groups.

---

## {ID}-1: {Capability Name}

### Happy Path

```gherkin
Scenario: {ID}-1.1 - {description}
  Given {precondition}
  When {trigger from EARS WHEN clause}
  Then {expected outcome from EARS response}
```

### Edge Cases

```gherkin
Scenario: {ID}-1.2 - {edge case description}
  When {edge case trigger}
  Then {expected behavior}
```

---

## {ID}-2: {Capability Name}

### Happy Path

```gherkin
Scenario: {ID}-2.1 - {description}
  Given {precondition}
  When {trigger}
  Then {expected outcome}
```

### Error Cases

```gherkin
Scenario: {ID}-2.2 - {error description}
  Given {precondition}
  When {trigger that causes error}
  Then {error response from EARS IF/THEN clause}
```

---

## Test ID Table

| ID | Spec Group | Scenario | Level | Test File |
|----|------------|----------|-------|-----------|
| {ID}-1.1 | {ID}-1 | {description} | Integration | {file} |
| {ID}-1.2 | {ID}-1 | {description} | Unit | {file} |
| {ID}-2.1 | {ID}-2 | {description} | E2E | {file} |

---

## Seed Data (from spec examples)

```typescript
export const seedData = {
  // Concrete data from spec example tables
};
```
