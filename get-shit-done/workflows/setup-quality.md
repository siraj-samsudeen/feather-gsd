<purpose>
Interactive configuration of quality enforcement features. Supports presets (minimal/standard/full) or custom selection. Updates .planning/config.json quality section.
</purpose>

<required_reading>
@~/.claude/get-shit-done/references/planning-config.md
</required_reading>

<process>

<step name="ensure_and_load_config">
Ensure config exists and load current state:

```bash
node ~/.claude/get-shit-done/bin/gsd-tools.js config-ensure-section
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js state load)
```

Parse quality config values from INIT JSON.
</step>

<step name="check_arguments">
Check if $ARGUMENTS contains a preset flag:

- `--minimal` → Apply minimal preset directly (skip interactive)
- `--standard` → Apply standard preset directly (skip interactive)
- `--full` → Apply full preset directly (skip interactive)
- `--off` → Disable all quality features directly (skip interactive)
- (none) → Continue to interactive selection
</step>

<step name="show_current">
Display current quality settings:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► QUALITY SETTINGS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Current:
| Feature              | Status |
|----------------------|--------|
| TDD Mode             | {off/basic/full} |
| EARS Specs           | {On/Off} |
| Two-Stage Review     | {On/Off} |
| Structured Feedback  | {On/Off} |
| UI Mockups           | {On/Off} |
| Design Exploration   | {On/Off} |
| Quality Checkpoints  | {On/Off} |
| Coverage Threshold   | {N}% |
```
</step>

<step name="select_preset">
Use AskUserQuestion:

```
questions: [
  {
    header: "Quality",
    question: "Which quality preset?",
    multiSelect: false,
    options: [
      { label: "Minimal", description: "Basic TDD + feedback tracking" },
      { label: "Standard (Recommended)", description: "TDD + specs + feedback + checkpoints" },
      { label: "Full", description: "All quality features enabled" },
      { label: "Custom", description: "Choose individual features" }
    ]
  }
]
```

**Preset values:**

| Feature | Minimal | Standard | Full |
|---------|---------|----------|------|
| tdd_mode | basic | basic | full |
| specs | false | true | true |
| two_stage_review | false | false | true |
| feedback | true | true | true |
| mockups | false | false | true |
| design_exploration | false | false | true |
| checkpoints | false | true | true |
| coverage_threshold | 100 | 100 | 100 |
</step>

<step name="custom_selection">
**Only if "Custom" selected.**

Use AskUserQuestion with 4 questions max per round:

**Round 1:**
```
questions: [
  {
    header: "TDD",
    question: "TDD enforcement level?",
    multiSelect: false,
    options: [
      { label: "Off", description: "No TDD enforcement" },
      { label: "Basic (Recommended)", description: "RED-GREEN-REFACTOR cycle, prompt-based" },
      { label: "Full", description: "Hook-enforced, blocks writes when tests fail" }
    ]
  },
  {
    header: "Specs",
    question: "Enable EARS specs and Gherkin derivation?",
    multiSelect: false,
    options: [
      { label: "Yes", description: "EARS specs → Gherkin scenarios → traceable tests" },
      { label: "No", description: "Plan directly from CONTEXT.md" }
    ]
  },
  {
    header: "Review",
    question: "Enable two-stage review (spec + code quality)?",
    multiSelect: false,
    options: [
      { label: "Yes", description: "Separate spec-compliance and code-quality reviewers" },
      { label: "No", description: "Standard self-check only" }
    ]
  },
  {
    header: "Feedback",
    question: "Enable structured feedback tracking?",
    multiSelect: false,
    options: [
      { label: "Yes (Recommended)", description: "Track issues in .feedback/ with priority" },
      { label: "No", description: "Issues captured as prose in UAT.md" }
    ]
  }
]
```

**Round 2 (if specs or full TDD):**
```
questions: [
  {
    header: "Mockups",
    question: "Enable HTML mockups before implementation?",
    multiSelect: false,
    options: [
      { label: "Yes", description: "Visual validation via Playwright/Chrome" },
      { label: "No", description: "Skip mockups" }
    ]
  },
  {
    header: "Explore",
    question: "Enable design exploration (2-3 approaches)?",
    multiSelect: false,
    options: [
      { label: "Yes", description: "Present trade-offs before locking decisions" },
      { label: "No", description: "Direct discussion without alternatives" }
    ]
  },
  {
    header: "Checkpoints",
    question: "Enable artifact gates before/after implementation?",
    multiSelect: false,
    options: [
      { label: "Yes (Recommended)", description: "Block execution if required artifacts missing" },
      { label: "No", description: "No artifact gates" }
    ]
  }
]
```
</step>

<step name="apply_config">
Merge quality settings into config.json:

```bash
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.tdd_mode "{value}"
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.specs {true/false}
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.two_stage_review {true/false}
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.feedback {true/false}
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.mockups {true/false}
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.design_exploration {true/false}
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.checkpoints {true/false}
node ~/.claude/get-shit-done/bin/gsd-tools.js config-set quality.coverage_threshold {N}
```

**If tdd_mode changed to "full":**
Suggest: "Run `/gsd:setup-tdd-hooks` to install TDD enforcement hooks."
</step>

<step name="confirm">
Display:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► QUALITY SETTINGS UPDATED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

| Feature              | Value |
|----------------------|-------|
| TDD Mode             | {off/basic/full} |
| EARS Specs           | {On/Off} |
| Two-Stage Review     | {On/Off} |
| Structured Feedback  | {On/Off} |
| UI Mockups           | {On/Off} |
| Design Exploration   | {On/Off} |
| Quality Checkpoints  | {On/Off} |
| Coverage Threshold   | {N}% |

Quick commands:
- /gsd:create-spec {N} — create EARS spec for a phase
- /gsd:derive-tests {N} — expand spec into Gherkin scenarios
- /gsd:feedback list — view feedback dashboard
- /gsd:setup-tdd-hooks — install TDD guard (if tdd_mode: full)
```
</step>

</process>

<success_criteria>
- [ ] Current quality config read
- [ ] User selected preset or custom options
- [ ] All quality.* fields updated in config.json
- [ ] If tdd_mode=full, user reminded about /gsd:setup-tdd-hooks
- [ ] Confirmation displayed with all settings
</success_criteria>
