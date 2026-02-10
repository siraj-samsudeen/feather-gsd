<purpose>
Create lightweight HTML mockups for visual validation before implementation. Start with text diagrams, then render as HTML in Chrome via Playwright.

Not production code — disposable artifacts for confirming layout, flow, and key interactions before writing real code.
</purpose>

<process>

<step name="load_state">
```bash
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js state load)
```

Parse `quality_mockups`.

**If quality_mockups is false:**
```
Mockups require quality.mockups = true.
Enable with: /gsd:setup-quality --minimal
```
Exit.
</step>

<step name="load_phase">
Phase number from $ARGUMENTS (required).

```bash
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js init phase-op "${PHASE}")
```

Parse JSON for: `phase_dir`, `phase_number`, `phase_name`, `phase_slug`, `padded_phase`, `has_context`.

**If has_context:** Read CONTEXT.md for design decisions.
**If SPEC.md exists:** Read for requirements.
</step>

<step name="text_diagram">
**Start with a text diagram** — fast, editable, shows structure.

Based on CONTEXT.md decisions and phase goal, sketch the key screens:

```
┌─────────────────────────────────┐
│  Header: App Name    [User ▼]  │
├─────────────────────────────────┤
│                                 │
│  ┌──────────┐  ┌──────────┐   │
│  │  Card 1  │  │  Card 2  │   │
│  │  Title   │  │  Title   │   │
│  │  Desc    │  │  Desc    │   │
│  └──────────┘  └──────────┘   │
│                                 │
│  ┌──────────┐  ┌──────────┐   │
│  │  Card 3  │  │  Card 4  │   │
│  └──────────┘  └──────────┘   │
│                                 │
├─────────────────────────────────┤
│  Footer / Navigation            │
└─────────────────────────────────┘
```

Present to user. Use AskUserQuestion:
- header: "Layout"
- question: "Does this layout match your vision? Should I render it as HTML?"
- options:
  - "Render HTML" — create interactive HTML mockup
  - "Adjust layout" — modify the text diagram first
  - "Good enough" — text diagram is sufficient, save and move on
</step>

<step name="render_html">
**Create HTML mockup** — single-file, self-contained, no build step.

Write to `docs/mockups/{phase_slug}.html`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mockup: Phase {N} - {Name}</title>
  <style>
    /* Inline styles — no external deps */
    /* Use system fonts, CSS grid/flexbox */
    /* Approximate the real design without pixel-perfection */
  </style>
</head>
<body>
  <!-- Mockup content -->
  <!-- Use placeholder data -->
  <!-- Include key interactions as static states -->
</body>
</html>
```

**Guidelines:**
- Single HTML file, all CSS inline
- System font stack (no Google Fonts)
- Placeholder data that tells a story (realistic names, counts)
- Show key states: empty, loaded, error, hover
- Use CSS grid/flexbox for layout
- Mobile-responsive if the feature is mobile-facing
- Include brief annotations as HTML comments: `<!-- Decision: cards layout per CONTEXT.md -->`

**Open in browser via Playwright:**
```
Navigate to file:///path/to/docs/mockups/{phase_slug}.html
Take screenshot
```

Present to user for feedback.
</step>

<step name="iterate">
Use AskUserQuestion to gather feedback:
- header: "Mockup"
- question: "How does this look?"
- options:
  - "Approved" — save and move on
  - "Adjust" — describe what to change
  - "Start over" — different approach

If "Adjust": ask what to change, modify HTML, re-render.
If "Start over": return to text_diagram.
If "Approved": proceed to save.

**Max 3 iterations** — mockups are disposable validation, not production design.
</step>

<step name="save">
**Save mockup artifacts:**

```bash
mkdir -p docs/mockups
```

Ensure HTML file is saved. Take a final screenshot:
```
docs/mockups/{phase_slug}.png
```

**If CONTEXT.md exists:** Add reference to mockup in CONTEXT.md specifics section.

Commit:
```bash
node ~/.claude/get-shit-done/bin/gsd-tools.js commit "docs(${padded_phase}): create UI mockup for ${phase_name}" --files "docs/mockups/${phase_slug}.html docs/mockups/${phase_slug}.png"
```

Present:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► MOCKUP SAVED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Phase: {N} - {Name}
Files: docs/mockups/{slug}.html, docs/mockups/{slug}.png

Next steps:
- /gsd:create-spec {N} — create EARS spec
- /gsd:plan-phase {N} — plan implementation
```
</step>

</process>

<success_criteria>
- [ ] Text diagram presented first (fast validation)
- [ ] HTML mockup is single-file, self-contained
- [ ] Mockup reflects CONTEXT.md decisions
- [ ] User approved the mockup (max 3 iterations)
- [ ] Files saved to docs/mockups/
- [ ] Committed with proper message
</success_criteria>
