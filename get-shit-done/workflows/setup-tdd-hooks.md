<purpose>
Install TDD Guard hooks that block implementation writes when tests are failing. Adapted from feather:setup-tdd-guard. Only applicable when quality.tdd_mode is "full".
</purpose>

<required_reading>
@~/.claude/get-shit-done/references/quality-enforcement.md
</required_reading>

<process>

<step name="check_prerequisites">
```bash
INIT=$(node ~/.claude/get-shit-done/bin/gsd-tools.js state load)
```

Parse `quality_tdd_mode` from INIT.

**If tdd_mode is not "full":**
```
TDD hooks require quality.tdd_mode = "full".

Current: {tdd_mode}

To enable: /gsd:setup-quality --full
```
Exit.

**Check for package.json:**
```bash
ls package.json
```

If missing: "TDD hooks require a Node.js project with package.json."
Exit.
</step>

<step name="detect_test_framework">
Check what test framework is already configured:

```bash
node -e "const p=require('./package.json'); console.log(JSON.stringify({deps: {...p.dependencies, ...p.devDependencies}, scripts: p.scripts}))"
```

Detect: vitest, jest, or none.

**If vitest:** Skip to step 4 (vitest config).
**If jest:** Inform user this requires vitest. Offer to install alongside.
**If none:** Install vitest from scratch.
</step>

<step name="install_dependencies">
```bash
npm install -D vitest @vitest/coverage-v8 tdd-guard-vitest
```

If the project uses React:
```bash
npm install -D jsdom @testing-library/react @testing-library/jest-dom
```
</step>

<step name="configure_vitest">
Create or update `vitest.config.ts` to include TDD Guard reporter:

```typescript
import { defineConfig } from "vitest/config";
import { VitestReporter } from "tdd-guard-vitest";

export default defineConfig({
  test: {
    reporters: ["default", new VitestReporter(__dirname)],
    coverage: {
      provider: "v8",
      reporter: ["text", "json"],
      thresholds: {
        lines: COVERAGE_THRESHOLD,
        branches: COVERAGE_THRESHOLD,
        functions: COVERAGE_THRESHOLD,
        statements: COVERAGE_THRESHOLD,
      },
    },
  },
});
```

Use `coverage_threshold` from quality config for threshold values.

**If vitest.config.ts already exists:** Merge VitestReporter and coverage thresholds into existing config. Do not overwrite other settings.
</step>

<step name="add_scripts">
Add to package.json if missing:

```json
{
  "scripts": {
    "test": "vitest",
    "test:run": "vitest run",
    "test:coverage": "vitest run --coverage"
  }
}
```

Do not overwrite existing test scripts — only add missing ones.
</step>

<step name="create_hook">
Create `.claude/settings.json` (or merge into existing):

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Write|Edit|MultiEdit",
        "hooks": [
          {
            "type": "command",
            "command": "tdd-guard"
          }
        ]
      }
    ]
  }
}
```

**If .claude/settings.json exists:** Merge the hook entry, do not overwrite existing hooks.
</step>

<step name="update_gitignore">
Add to .gitignore if not present:

```
# Test coverage
coverage/

# TDD Guard runtime data
.claude/tdd-guard/
```
</step>

<step name="smoke_test">
Run preflight checks:

```bash
echo "=== TDD Guard Pre-flight ===" && PASS=0 && FAIL=0 && \
check() { if eval "$2" > /dev/null 2>&1; then echo "  PASS: $1"; PASS=$((PASS+1)); else echo "  FAIL: $1"; FAIL=$((FAIL+1)); fi; } && \
check "vitest installed" "[ -d node_modules/vitest ]" && \
check "tdd-guard-vitest installed" "[ -d node_modules/tdd-guard-vitest ]" && \
check "@vitest/coverage-v8 installed" "[ -d node_modules/@vitest/coverage-v8 ]" && \
check "vitest config exists" "[ -f vitest.config.ts ] || [ -f vitest.config.js ]" && \
check "VitestReporter in config" "grep -q 'VitestReporter' vitest.config.ts 2>/dev/null || grep -q 'VitestReporter' vitest.config.js 2>/dev/null" && \
check "Hook config exists" "[ -f .claude/settings.json ]" && \
check "Hook matcher includes Write|Edit" "grep -q 'Write|Edit' .claude/settings.json" && \
check "tdd-guard CLI available" "[ -x node_modules/.bin/tdd-guard ]" && \
check "coverage/ in .gitignore" "grep -q 'coverage/' .gitignore" && \
echo "" && echo "Results: $PASS passed, $FAIL failed"
```

If any check fails, fix the corresponding step before continuing.

**Inform user:** "Restart Claude Code session (`/clear` or exit/reopen) for hooks to take effect."
</step>

<step name="confirm">
```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 GSD ► TDD HOOKS INSTALLED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

TDD Guard will block implementation writes when tests are failing.

Coverage threshold: {N}%

Restart Claude Code for hooks to take effect.

How it works:
1. Write a failing test (RED)
2. Run tests — vitest reports state to .claude/tdd-guard/data/test.json
3. Try to write implementation — hook allows (tests exist but fail)
4. Implement until tests pass (GREEN)
5. Refactor (tests must stay passing)

Override: git commit --no-verify to bypass coverage check.
```
</step>

</process>

<success_criteria>
- [ ] tdd_mode is "full" (or exited with message)
- [ ] vitest + tdd-guard-vitest installed
- [ ] vitest config has VitestReporter and coverage thresholds
- [ ] .claude/settings.json has PreToolUse hook
- [ ] .gitignore updated
- [ ] Preflight checks all pass
- [ ] User reminded to restart session
</success_criteria>
