<purpose>
Work through feedback items (bugs, UX tweaks) with TDD discipline but without spec/gherkin ceremony. Each item gets RED-GREEN treatment: write a failing test that reproduces the issue, then fix it.
</purpose>

<required_reading>
@~/.claude/get-shit-done/references/quality-enforcement.md
</required_reading>

<process>

<step name="load_state">
```bash
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js state load)
```

Parse `quality_feedback`, `quality_tdd_mode`.

**If quality_feedback is false:**
```
Polish queue requires quality.feedback = true.
Enable with: /gsd:setup-quality --minimal
```
Exit.
</step>

<step name="select_items">
**If specific ID in $ARGUMENTS:** Find that item.

**Otherwise:** List open items sorted by priority:

```bash
ls .feedback/open/*.md 2>/dev/null
```

Parse frontmatter from each, sort by priority (P0 first).

Present:
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► POLISH QUEUE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

{N} items in queue

| # | ID | Priority | Description |
|---|-----|----------|-------------|
| 1 | BUG-001 | P0 | {description} |
| 2 | UX-002 | P1 | {description} |
```

Use AskUserQuestion to select which item(s) to work on.
</step>

<step name="work_item">
For each selected item:

**1. Move to in-progress:**
```bash
mv .feedback/open/{file} .feedback/in-progress/
```

**2. Understand the issue:**
Read the feedback item. Identify affected files and expected behavior.

**3. RED — Write failing test:**
Create a test that reproduces the issue:
```bash
# Create test file
# Write test that fails because of the bug
# Run test — must fail
```

Commit: `test: add failing test for {ID}`

**4. GREEN — Fix the issue:**
Implement the fix. Run test — must pass.

Commit: `fix: resolve {ID} — {short description}`

**5. Verify:**
Run full test suite to ensure no regressions:
```bash
npm run test:run
```

**6. Move to resolved:**
```bash
mv .feedback/in-progress/{file} .feedback/resolved/
```

Update frontmatter: `status: resolved`
Add resolution description to the file.

**7. Regenerate dashboard:**
Scan and update `.feedback/INDEX.md`.
</step>

<step name="summary">
After working through items:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► POLISH COMPLETE
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Resolved: {N} items
Remaining: {M} items in queue

[List resolved items with commit hashes]
```
</step>

</process>

<success_criteria>
- [ ] Items selected from queue
- [ ] Each item: failing test written → fix implemented → test passes
- [ ] Items moved through open → in-progress → resolved
- [ ] INDEX.md updated
- [ ] No test regressions
</success_criteria>
