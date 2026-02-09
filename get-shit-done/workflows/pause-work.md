<purpose>
Create `.continue-here.md` handoff file to preserve complete work state across sessions. Also updates `docs/DIALOGUE.md` with a permanent session record before committing. Enables seamless resumption with full context restoration.
</purpose>

<required_reading>
Read all files referenced by the invoking prompt's execution_context before starting.
</required_reading>

<process>

<step name="detect">
Find current phase directory from most recently modified files:

```bash
# Find most recent phase directory with work
ls -lt .planning/phases/*/PLAN.md 2>/dev/null | head -1 | grep -oP 'phases/\K[^/]+'
```

If no active phase detected, ask user which phase they're pausing work on.
</step>

<step name="gather">
**Collect complete state for handoff:**

1. **Current position**: Which phase, which plan, which task
2. **Work completed**: What got done this session
3. **Work remaining**: What's left in current plan/phase
4. **Decisions made**: Key decisions and rationale
5. **Blockers/issues**: Anything stuck
6. **Mental context**: The approach, next steps, "vibe"
7. **Files modified**: What's changed but not committed

Ask user for clarifications if needed via conversational questions.
</step>

<step name="write">
**Write handoff to `.planning/phases/XX-name/.continue-here.md`:**

```markdown
---
phase: XX-name
task: 3
total_tasks: 7
status: in_progress
last_updated: [timestamp from current-timestamp]
---

<current_state>
[Where exactly are we? Immediate context]
</current_state>

<completed_work>

- Task 1: [name] - Done
- Task 2: [name] - Done
- Task 3: [name] - In progress, [what's done]
</completed_work>

<remaining_work>

- Task 3: [what's left]
- Task 4: Not started
- Task 5: Not started
</remaining_work>

<decisions_made>

- Decided to use [X] because [reason]
- Chose [approach] over [alternative] because [reason]
</decisions_made>

<blockers>
- [Blocker 1]: [status/workaround]
</blockers>

<context>
[Mental state, what were you thinking, the plan]
</context>

<next_action>
Start with: [specific first action when resuming]
</next_action>
```

Be specific enough for a fresh Claude to understand immediately.

Use `current-timestamp` for last_updated field. You can use init todos (which provides timestamps) or call directly:
```bash
timestamp=$(node ~/.claude/get-shit-done/bin/gsd-tools.js current-timestamp full --raw)
```
</step>

<step name="dialogue">
**Update `docs/DIALOGUE.md` with a permanent session record.**

If `docs/DIALOGUE.md` does not exist, skip this step.

1. **Read** `docs/DIALOGUE.md` to find the last session number (e.g., "## Session 5: ...").
2. **Read** recent git log since the last dialogue entry's commits to understand what was done:
   ```bash
   git log --oneline -20
   ```
3. **Auto-draft** a new session entry using the state gathered in the "gather" step. Format:

```markdown
## Session [N+1]: [Short description of what happened] ([date])

### What Changed

- [Bullet points from completed work + decisions made this session]

### GSD Workflow State

**Current position:** [Phase X, Task Y of Z]
**Next step:** [What to do on resume]

**Commits this session:**
```
[list of commit hashes + messages from this session]
```

---
*Last updated: [date] — session [N+1], [brief status]*
```

4. **Show the draft to the user** via AskUserQuestion: "Here's the DIALOGUE.md entry I'll append. Approve or edit?"
5. **On approval**, append the entry to `docs/DIALOGUE.md` (replace the existing last-updated footer line, then add the new entry + new footer).

**Key rules:**
- Don't duplicate content already in committed files (ROADMAP, REQUIREMENTS) — reference them instead.
- Keep entries concise — developer diary, not a transcript.
- If no meaningful decisions or work happened, keep it to 3-4 lines.
</step>

<step name="commit">
```bash
# Include both handoff and dialogue in the commit
node ~/.claude/get-shit-done/bin/gsd-tools.js commit "wip: [phase-name] paused at task [X]/[Y]" --files .planning/phases/*/.continue-here.md docs/DIALOGUE.md
```

If `docs/DIALOGUE.md` was not updated (file doesn't exist or step was skipped), omit it from --files.
</step>

<step name="confirm">
```
✓ Handoff created: .planning/phases/[XX-name]/.continue-here.md

Current state:

- Phase: [XX-name]
- Task: [X] of [Y]
- Status: [in_progress/blocked]
- Committed as WIP

To resume: /gsd:resume-work

```
</step>

</process>

<success_criteria>
- [ ] .continue-here.md created in correct phase directory
- [ ] All sections filled with specific content
- [ ] docs/DIALOGUE.md updated with session entry (if file exists)
- [ ] User approved the dialogue entry before it was written
- [ ] Committed as WIP (handoff + dialogue)
- [ ] User knows location and how to resume
</success_criteria>
