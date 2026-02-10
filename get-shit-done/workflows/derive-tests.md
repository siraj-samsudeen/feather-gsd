<purpose>
Expand EARS spec into Gherkin scenarios. Spawns gsd-test-deriver agent, presents output for user review (mandatory gate), saves {phase}-GHERKIN.md.
</purpose>

<required_reading>
@~/.claude/get-shit-done/references/ears-spec-format.md
</required_reading>

<process>

<step name="load_state">
```bash
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js init plan-phase "$PHASE" --include spec,context,requirements)
```

Parse: `quality_specs`, `phase_found`, `phase_dir`, `phase_name`, `has_spec`, `spec_content`, `has_gherkin`, `context_content`, `requirements_content`.

Resolve model:
```bash
MODEL=$(node ~/.claude/get-shit-done/bin/gsd-tools.js resolve-model gsd-test-deriver)
```

**If quality_specs is false:**
```
Gherkin derivation requires quality.specs = true.
Enable with: /gsd:setup-quality --standard
```
Exit.

**If no SPEC.md:** Suggest `/gsd:create-spec {N}` first.

**If GHERKIN.md already exists:**
Use AskUserQuestion:
- "Gherkin scenarios already exist. Re-derive or keep?"
- Options: "Re-derive" / "Keep existing"
</step>

<step name="spawn_deriver">
```
Task(prompt="
First, read ~/.claude/agents/gsd-test-deriver.md for your role and instructions.

<spec_content>
{spec_content}
</spec_content>

<context>
{context_content or 'No CONTEXT.md available'}
</context>

<requirements>
{requirements_content or 'No REQUIREMENTS.md available'}
</requirements>

<output>
phase_dir: {phase_dir}
phase: {padded_phase}
Write to: {phase_dir}/{padded_phase}-GHERKIN.md
Use template: ~/.claude/get-shit-done/templates/gherkin.md
</output>
", subagent_type="gsd-test-deriver", model="{MODEL}", description="Derive Gherkin from spec")
```
</step>

<step name="present_for_review">
Read the generated GHERKIN.md and present to user:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► GHERKIN SCENARIOS: Phase {N}
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{scenario_count} scenarios across {group_count} groups

[Show grouped Gherkin — clean format, no ID tables in conversation]
```

**MANDATORY GATE — User must approve before implementation:**

Use AskUserQuestion:
- header: "Gherkin"
- question: "Do these scenarios capture what should be tested?"
- options:
  - "Approve" — Save and commit
  - "Add scenarios" — I want to add or modify scenarios
  - "Re-derive" — Start over with different approach

**If "Add scenarios":** Get user input, edit GHERKIN.md, re-present.
**If "Re-derive":** Re-spawn deriver with user feedback.
**If "Approve":**

```bash
node ~/.claude/get-shit-done/bin/gsd-tools.js commit "docs({phase}): derive Gherkin scenarios" --files {phase_dir}/{phase}-GHERKIN.md
```
</step>

<step name="next_steps">
```
Gherkin approved and committed.

Next:
- /gsd:plan-phase {N} — plan implementation (spec + Gherkin inform planning)
```
</step>

</process>

<success_criteria>
- [ ] SPEC.md exists and was read
- [ ] gsd-test-deriver spawned with spec content
- [ ] Gherkin scenarios cover all spec capability groups
- [ ] Edge cases added beyond what spec covers
- [ ] IDs trace to spec groups
- [ ] User reviewed and approved scenarios (GATE)
- [ ] {phase}-GHERKIN.md written and committed
</success_criteria>
