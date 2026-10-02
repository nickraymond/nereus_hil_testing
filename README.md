# nereus_hil_testing

Nereus hardware-in-the-loop (HIL) testing: one format for every project's bench tests,
so a test written by any session can be run by Nick (or the bench owner) from its
commands alone, and leaves a run folder that proves PASS or FAIL per criterion.

```text
docs/       SPEC (goal, bench inventory, hard constraints) · TRACKER (rules + sprints) ·
            DESIGN (layout, HIL-ready checklist, decisions, run index) · DEV_LOG · PROMPTS
hil/        the HIL format (README), gates/, tests/, procedures/, tools/, templates/
runs/       runs/<test>_<YYYYMMDD>/ — RESULTS.md + run_manifest.json + evidence
.claude/    repo-local skills: /agent-entry, /capture-task
```

## Hand a test over

Write `hil/tests/<test>.md` so it meets the HIL-ready checklist (`docs/DESIGN.md`):
spec, code ref, criteria table, inputs, restore, budget, operator-runnable.
Prompt: `docs/PROMPTS.md` §5.

## Run a test

```bash
source hil/hil.env
export HIL_RUN_DIR=$(hil/tools/hil_new_run.sh <test> <TEST_ID> <unit> ...)
```

Then follow the spec's steps; fill `$HIL_RUN_DIR/RESULTS.md` (one verdict per criterion,
each citing a file in the run folder) and add a line to the run index in `docs/DESIGN.md`.
(The tools arrive with the R1 port, TRACKER S0.)

## Working here with an agent

Start every session with `/agent-entry` (or the Rules for Agents in `docs/TRACKER.md`).
Process docs come from Nick's `template_LLM_project`.
