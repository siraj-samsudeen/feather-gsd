<ears_spec_format>

# EARS Spec Format Reference

EARS (Easy Approach to Requirements Syntax) provides structured natural language patterns for writing unambiguous requirements. Used with `quality.specs: true`.

## Syntax Patterns

| Pattern | Keyword | Template | Use For |
|---------|---------|----------|---------|
| Event-driven | WHEN | WHEN [trigger] THE SYSTEM SHALL [response] | User actions, events |
| State-driven | WHILE | WHILE [state] THE SYSTEM SHALL [behavior] | Behavior during states |
| Unwanted | IF/THEN | IF [condition] THEN THE SYSTEM SHALL [response] | Errors, failures |
| Ubiquitous | (none) | THE SYSTEM SHALL [constraint] | Always-true rules |
| Optional | WHERE | WHERE [feature] THE SYSTEM SHALL [behavior] | Feature flags |

## Capability Groups

Group requirements by **user capability**, not technical component. Each group gets a short ID.

```
GOOD (user perspective):
  TM-1: Task Creation
  TM-2: Task Completion
  TM-3: Task List

BAD (technical perspective):
  TM-1: TaskForm Component
  TM-2: Status Toggle API
  TM-3: ListView Query
```

**ID format:** Short feature prefix + number (TM-1, AUTH-2, CP-3).
IDs trace through to Gherkin scenarios, test names, bugs, and sign-off.

## Example Tables

Every capability group MUST have at least one example table. Examples serve triple duty:
1. **Stakeholder sign-off** — "Yes, that's what I mean"
2. **Test data** — becomes fixtures and seed data
3. **Disambiguation** — resolves ambiguity better than prose

**Formats:**

For creation (input → result):
```markdown
| Input | Result |
|-------|--------|
| "Buy milk" | Task created, incomplete |
```

For state changes (before → action → after):
```markdown
| Before | Action | After |
|--------|--------|-------|
| "Buy milk" (incomplete) | toggle | "Buy milk" (complete) |
```

For calculations (inputs → output):
```markdown
| Subtotal | Coupon | Discount | Total |
|----------|--------|----------|-------|
| $100 | 20% off | $20 | $80 |
```

## Complete Example

```markdown
# Feature: TodoList

**Goal:** Let users track tasks with create, view, toggle, and delete.

**Scope**
- In: Create tasks, view list, toggle completion, delete tasks
- Out: Due dates, priorities, multiple lists

---

## TL-1: Create Task

- WHEN I type a task and press Enter, THE SYSTEM SHALL add it to my list

**Examples:**
| Input | Result |
|-------|--------|
| "Buy milk" | Task appears, unchecked |
| "Call dentist" | Task appears, unchecked |

---

## TL-2: View Tasks

- WHEN I open the app, THE SYSTEM SHALL show all my tasks
- WHILE no tasks exist, THE SYSTEM SHALL show "No todos yet"

**Examples:**
| State | Display |
|-------|---------|
| No tasks | "No todos yet" message |
| Has tasks | List with checkboxes |

---

## TL-3: Toggle Complete

- WHEN I click a task's checkbox, THE SYSTEM SHALL toggle its completion

**Examples:**
| Before | Action | After |
|--------|--------|-------|
| "Buy milk" unchecked | click | "Buy milk" checked |
| "Buy milk" checked | click | "Buy milk" unchecked |

---

## Rules

**Input Validation**
- IV1: Task text must be 1-500 characters

**Data Model**

**T1: todos**
- text (string)
- completed (yes/no)

---

## Out of Scope

- Due dates and priorities
- Multiple lists per user
- Sharing tasks between users
```

## Validation Checklist

Review each capability group against EARS patterns:

| Pattern | Question |
|---------|----------|
| **WHEN** | All user actions covered? |
| **WHILE** | State-dependent behavior identified? |
| **IF/THEN** | Error cases user cares about covered? |
| **Ubiquitous** | Always-true constraints stated? |
| **WHERE** | Feature variations noted? |
| Examples | At least one example table per group? |

## Anti-Patterns

| Don't | Do |
|-------|-----|
| Flat list of 15 EARS criteria | Group by capability with IDs |
| EARS criteria without examples | Every group has example table |
| Vague criteria ("system handles errors") | Specific EARS + concrete data |
| Technical group names ("API Integration") | User capability names ("Task Creation") |
| API endpoints in spec | Action names, map later |
| Technical types (uuid, jsonb) | Business terms (unique ID, list) |
| "Record must exist for delete" | Omit obvious behavior |
| "Must be authenticated" | Assume auth, state exceptions |

## ID Reference

| Prefix | Meaning |
|--------|---------|
| XX-N | Capability group (EARS criteria + examples) |
| BV | Business Validation |
| C | Calculation |
| P | Permission |
| IV | Input Validation |
| S | State Rule |
| T | Table |

Mark critical rules with `(critical)` suffix for extra test coverage.

## Where Specs Live in GSD

Specs live alongside other phase artifacts:

```
.planning/phases/XX-name/
  {phase}-CONTEXT.md    ← user decisions
  {phase}-SPEC.md       ← EARS spec (this format)
  {phase}-GHERKIN.md    ← derived test scenarios
  {phase}-RESEARCH.md   ← domain research
  {phase}-PLAN.md       ← execution plan
```

</ears_spec_format>
