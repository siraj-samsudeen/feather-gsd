<purpose>
Create an EARS specification for a phase. Reads CONTEXT.md, REQUIREMENTS.md, and ROADMAP.md to produce a complete {phase}-SPEC.md with capability groups, EARS criteria, and example tables.
</purpose>

<required_reading>
@~/.claude/get-shit-done/references/ears-spec-format.md
@~/.claude/get-shit-done/templates/spec.md
</required_reading>

<process>

<step name="load_state">
```bash
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js init plan-phase "$PHASE" --include context,requirements,roadmap)
```

Parse: `quality_specs`, `phase_found`, `phase_dir`, `phase_name`, `has_context`, `context_content`, `requirements_content`, `roadmap_content`.

**If quality_specs is false:**
```
EARS specs require quality.specs = true.

Enable with: /gsd:setup-quality --standard
```
Exit.

**If phase not found:** Error with phase listing.

**If no CONTEXT.md:** Suggest `/gsd:discuss-phase {N}` first.
</step>

<step name="extract_phase_scope">
From ROADMAP.md, extract:
- Phase goal
- Mapped requirements (REQ-IDs)
- Success criteria

From CONTEXT.md, extract:
- Locked decisions
- Phase boundary
- Deferred items (→ Out of Scope)
</step>

<step name="draft_spec">
Using the EARS spec template and reference:

1. **Identify capability groups** from requirements and context
   - Group by user capability, not technical component
   - Assign IDs: {PREFIX}-1, {PREFIX}-2, etc.
   - PREFIX = 2-3 letter abbreviation of feature

2. **Write EARS criteria** for each group
   - WHEN for user actions
   - WHILE for state-dependent behavior
   - IF/THEN for error cases
   - Ubiquitous for always-true constraints

3. **Create example tables** for each group
   - Use concrete data from CONTEXT.md discussions
   - Use data from REQUIREMENTS.md examples
   - Cover happy path and key edge cases

4. **Add rules sections** (only if applicable)
   - Business validation, calculations, permissions, input validation, state rules

5. **Define data model** if the phase introduces new entities

6. **List Out of Scope** from CONTEXT.md deferred items
</step>

<step name="validate">
Run EARS completeness check:

For each capability group, verify:
- [ ] At least one EARS criterion
- [ ] At least one example table
- [ ] WHEN covers all user actions
- [ ] IF/THEN covers error cases users care about
- [ ] IDs are unique and sequential

Cross-check against REQUIREMENTS.md:
- [ ] Every mapped requirement has at least one capability group
- [ ] No orphan requirements (mapped but not covered)
</step>

<step name="present_and_save">
Present the spec to the user, grouped by capability:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► SPEC: Phase {N} — {Phase Name}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{N} capability groups | {M} EARS criteria | {K} examples

[Show full spec content]
```

Use AskUserQuestion:
- header: "Spec"
- question: "Does this spec capture what Phase {N} should deliver?"
- options:
  - "Approve" — Save and commit
  - "Adjust" — Tell me what to change
  - "Review full file" — Show raw markdown

**If "Adjust":** Incorporate feedback, re-present.
**If "Approve":**

Write to `{phase_dir}/{phase}-SPEC.md`.

```bash
node ~/.claude/get-shit-done/bin/gsd-tools.js commit "docs({phase}): create EARS spec" --files {phase_dir}/{phase}-SPEC.md
```
</step>

<step name="next_steps">
```
Spec saved. Next:

/gsd:derive-tests {N} — expand spec into Gherkin scenarios
/gsd:plan-phase {N} — plan implementation (will auto-derive tests if spec exists)
```
</step>

</process>

<success_criteria>
- [ ] CONTEXT.md read for user decisions
- [ ] REQUIREMENTS.md read for mapped requirements
- [ ] Capability groups identified with IDs
- [ ] EARS criteria written for each group
- [ ] Example tables for each group
- [ ] EARS completeness validated
- [ ] User approved spec
- [ ] {phase}-SPEC.md written and committed
</success_criteria>
