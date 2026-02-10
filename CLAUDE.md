# Feather GSD — Fork of Get Shit Done

## What this repo is

A fork of [glittercowboy/get-shit-done](https://github.com/glittercowboy/get-shit-done) with local customizations. The fork is the source of truth for `~/.claude/` installation — not upstream npm.

## Git remotes

- `origin` — `github.com/siraj-samsudeen/feather-gsd` (this fork)
- `upstream` — `github.com/glittercowboy/get-shit-done` (original)

## Key customizations (vs upstream)

1. **Outcome-first roadmapper** (`agents/gsd-roadmapper.md`) — deliverables lead with what the user/developer can do, tools in parentheses
2. **Fork-based update workflow** (`get-shit-done/workflows/update.md`) — `/gsd:update` syncs upstream into this fork, builds, and installs from here
3. **GitHub distribution** — `hooks/dist/` is committed (upstream ignores it; they publish via npm which includes build artifacts)
4. **Deploy scripts** in `package.json` — `npm run deploy` and `npm run sync-upstream`

## How to make changes

1. Edit files in this repo (agents, commands, references, templates, workflows)
2. Run `npm run deploy` to build hooks and install to `~/.claude/`
3. Restart Claude Code to pick up changes
4. Commit to this repo and push to origin

## How to sync upstream updates

```bash
npm run sync-upstream
```

This runs: `git fetch upstream && git merge upstream/main && npm run build:hooks && node bin/install.js --claude --global`

If there are merge conflicts, resolve them manually, then run `npm run deploy`.

## `.gitignore` difference

Upstream ignores `hooks/dist/` (build artifact published via npm). This fork commits it so that `npx github:siraj-samsudeen/feather-gsd` works without a build step.

When syncing upstream, keep our `.gitignore` if there's a conflict on this line.

## Do not modify

- `bin/install.js` — The installer. Changes here should go upstream via PR.
- `hooks/` source files — These are upstream's hook implementations. Modify via PR.
- Files outside `agents/`, `commands/`, `get-shit-done/`, `package.json`, `.gitignore` should generally stay in sync with upstream.

## Safe to customize

- `agents/gsd-*.md` — Agent behavior and instructions
- `commands/gsd/*.md` — Slash command definitions
- `get-shit-done/workflows/*.md` — Workflow orchestration
- `get-shit-done/references/*.md` — Reference documentation
- `get-shit-done/templates/*.md` — Template files

## Quality enforcement (Feather integration)

This fork adds a quality enforcement layer from Feather — all features gated by config, disabled by default. Existing GSD behavior is unchanged unless you enable quality features.

### Quick start

```
/gsd:setup-quality          # Interactive — choose minimal/standard/full preset
/gsd:setup-quality --full   # Enable everything
```

### Features (all config-gated)

| Feature | Config key | Commands |
|---------|-----------|----------|
| EARS Specs | `quality.specs` | `/gsd:create-spec`, `/gsd:derive-tests` |
| Three-tier TDD | `quality.tdd_mode` | off / basic / full |
| Two-stage review | `quality.two_stage_review` | Automatic in executor |
| Structured feedback | `quality.feedback` | `/gsd:feedback`, `/gsd:polish` |
| UI mockups | `quality.mockups` | `/gsd:mockup` |
| Design exploration | `quality.design_exploration` | Automatic in discuss-phase |
| Quality checkpoints | `quality.checkpoints` | CP1 (pre-impl) + CP2 (post-impl) |
| Coverage threshold | `quality.coverage_threshold` | Enforced in `tdd_mode: "full"` |

### Config location

`.planning/config.json` → `quality` section. All values default to off/false.

### New agents

- `gsd-test-deriver` — Expands EARS specs into Gherkin scenarios
- `gsd-spec-reviewer` — Reviews implementation against spec
- `gsd-code-reviewer` — Reviews code quality

### New artifact locations

- `.planning/phases/XX-name/{phase}-SPEC.md` — EARS spec
- `.planning/phases/XX-name/{phase}-GHERKIN.md` — Gherkin scenarios
- `.feedback/` — Structured feedback items (open/in-progress/resolved)
- `docs/mockups/` — HTML mockups
