<purpose>
Manage structured feedback items in `.feedback/` directory. Supports list, add, resolve, and dashboard regeneration.
</purpose>

<process>

<step name="check_config">
```bash
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js state load)
```

Parse `quality_feedback`.

**If quality_feedback is false:**
```
Structured feedback requires quality.feedback = true.
Enable with: /gsd:setup-quality --minimal
```
Exit.
</step>

<step name="ensure_directory">
```bash
mkdir -p .feedback/open .feedback/in-progress .feedback/resolved
```
</step>

<step name="route_subcommand">
Parse $ARGUMENTS for subcommand: `list`, `add`, `resolve {ID}`, `dashboard`.

Default (no args or `list`): Show dashboard.
</step>

<step name="list_dashboard">
If `.feedback/INDEX.md` exists, show it.

Otherwise, scan `.feedback/` directories and generate dashboard:

```bash
ls .feedback/open/*.md 2>/dev/null | wc -l
ls .feedback/in-progress/*.md 2>/dev/null | wc -l
ls .feedback/resolved/*.md 2>/dev/null | wc -l
```

Present:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► FEEDBACK DASHBOARD
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Open: {N} | In Progress: {M} | Resolved: {K}

[Table of open items sorted by priority]
```
</step>

<step name="add_item">
Use AskUserQuestion:

```
questions: [
  {
    header: "Type",
    question: "What kind of issue?",
    multiSelect: false,
    options: [
      { label: "Bug", description: "Something is broken" },
      { label: "UX", description: "Works but feels wrong" },
      { label: "Performance", description: "Too slow" },
      { label: "Polish", description: "Minor improvement" }
    ]
  },
  {
    header: "Priority",
    question: "How urgent?",
    multiSelect: false,
    options: [
      { label: "P0 - Critical", description: "Blocks usage" },
      { label: "P1 - High", description: "Major issue, needs fix soon" },
      { label: "P2 - Medium", description: "Should fix, not urgent" },
      { label: "P3 - Low", description: "Nice to fix when convenient" }
    ]
  }
]
```

Then ask for description (freeform).

Generate ID: `{TYPE}-{NNN}` where NNN is next available number.

Write to `.feedback/open/{ID}-{slug}.md` using feedback-item template.

Regenerate INDEX.md.
</step>

<step name="resolve_item">
Find item file by ID across all directories.

Move from current directory to `.feedback/resolved/`.

Update frontmatter: `status: resolved`.

Ask for resolution description (freeform), add to Resolution section.

Regenerate INDEX.md.
</step>

<step name="regenerate_dashboard">
Scan all `.feedback/` subdirectories.

Parse frontmatter from each `.md` file.

Generate INDEX.md using feedback-index template with:
- Summary counts by priority and status
- Open items table (sorted by priority)
- In-progress items table
- Recently resolved items (last 10)

Write to `.feedback/INDEX.md`.
</step>

</process>

<success_criteria>
- [ ] .feedback/ directory structure exists
- [ ] Subcommand correctly routed
- [ ] Items created with proper IDs and templates
- [ ] INDEX.md reflects current state
</success_criteria>
