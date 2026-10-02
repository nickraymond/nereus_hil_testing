# DESIGN.md — Architecture & Decisions (as-built)

*What it did / how it's shaped. Agents append; never silently rewrite history.*
*Last updated: 2026-10-02*

## System topology

```text
product repo (e.g. bm_cam_legacy)          nereus_hil_testing (this repo)
  dev session writes the test spec  ──►     hil/tests/<test>.md      HIL-ready handover (§HIL-ready)
  code under test: merged sha               hil/gates/<gate>.md      release gates
                                            hil/procedures/          bench procedures that are not gates
                                            hil/tools/  ──ssh/CLI──► bench (nereus000, Spotters, units)
                                            runs/<test>_<YYYYMMDD>/  evidence + RESULTS.md (PASS/FAIL per row)
```

Tests reach the hardware only over ssh / console / API from `hil/tools/`. Nothing here
imports product code; a test names the product sha it runs against (HIL-ready item 2).

## Key designs

### Repo layout

```text
CLAUDE.md, README.md, docs/      process docs (SPEC / TRACKER / DESIGN / DEV_LOG / PROMPTS)
.claude/skills/                  agent-entry, capture-task (repo-local; never copy to ~/.claude)
hil/                             the HIL format, copied from the Test Engineer's hil/ @ 7a867d9 + differences below
  README.md                      the format (HIL-ready, criteria table, run folder, RESULTS, bench rules)
  hil.env.example                hosts / ids the tools read; hil.env is gitignored
  gates/                         one file per release gate (criteria table, steps, restore)
  procedures/                    bench procedures that are not gates (takeover, probes)
  tests/                         NEW (D3): handed-over tests that are not release gates
  tools/                         standalone scripts, each with the header block (purpose, inputs, outputs, example, limits)
  templates/                     RESULTS.md, run_manifest.json
runs/<test>_<YYYYMMDD>/          one self-contained folder per run (created by hil_new_run.sh)
  RESULTS.md                     verdict line, criteria (measured + verdict), steps, findings, restore, not tested
  run_manifest.json              schema hil_run_manifest/1
  commands.log gate.log steps.log
  snapshots/ console/ api/ pulled/ analysis/
```

Where each thing goes:

| thing | home | written by |
|---|---|---|
| test spec (purpose, units, preconditions, steps, restore) | `hil/tests/<test>.md` or `hil/gates/<gate>.md` | dev session (test) / Test Engineer (gate) |
| gate criteria | the criteria table inside that spec | same |
| tools | `hil/tools/` | whoever needs them; header block required |
| run evidence | `runs/<test>_<YYYYMMDD>/` | operator (Nick or the bench-owner session) |
| results | `runs/<test>_<YYYYMMDD>/RESULTS.md` | operator |
| run index | §Run index below, one line per run | operator, at run end |

The format's source of truth is `hil/README.md` here (ported 2026-10-02 from bm_cam_legacy
7a867d9, D10). Changes to it are made here, by PR.

### HIL-ready checklist (handover contract)

A session hands a test over when the spec has all of these. Items 1–6 are the Test
Engineer's `hil/README.md` §1, unchanged; 7 is ours (D8), added to `hil/README.md` §1 at the port.

| # | item | must contain |
|---|---|---|
| 1 | Spec | one markdown file: purpose, units, preconditions, steps, restore |
| 2 | Code ref | merged sha (or named branch tip) the unit must run + a read-only command that proves it is deployed |
| 3 | Criteria table | `id / criterion / PASS when / evidence`; each row a measurement with a threshold; evidence names a file in the run folder; never "works" / "looks OK" |
| 4 | Inputs | every command / config / file sent, written out, with its lane (console or Sofar) |
| 5 | Restore | how each unit returns to its pre-test state + the read-back that proves it (config hash, crontab, bus) |
| 6 | Budget | wall time, cellular messages, Nick's hands (box, power, Render) |
| 7 | Operator-runnable | every step is a copy-pastable command or a named `hil/tools/` script; Nick can run it with no agent in the loop |

Missing a row → sent back with the missing row named. Verdicts: `PASS`, `FAIL`, `BLOCKED`
(why), `N/A` (reason). A test PASSes only when every row is PASS or an agreed N/A.

### Differences from the Test Engineer's `hil/` (found at ec2a449; applied at the port from 7a867d9)

| # | difference | recommendation |
|---|---|---|
| 1 | `hil/README.md` calls the target repo `nereus_HIL_tooling` | one-line rename on port |
| 2 | Evidence lives in the product repo `runs/`, where `*.log` / `*.csv` are gitignored (force-add) | here: logs and CSV are tracked (no `-f`); video (`*.mp4 *.h264`) ignored; full-size media stays off git, sha256 in the manifest. R1 runs stay in bm_cam_legacy; do not move them |
| 3 | No home for a non-gate test a dev session hands over (its README §1 example lives in the product repo `sprints/`) | add `hil/tests/` (D3); gates stay in `hil/gates/` |
| 4 | Gate files are not release-scoped (`G3_api_hard_mode.md`) | **decided (D7):** copy flat for R1; from R2 on name gates `R2_G1_<slug>.md` so `hil/gates/` stays flat and the RESULTS template glob still works |
| 5 | Tools are bmcam-specific and read product-repo files (`docs/bmcam_config_catalog.json`, `tools/rc_field_update.sh`) by relative path | keep flat for now (one product); add `HIL_PRODUCT_REPO` to `hil.env` and pass `--catalog $HIL_PRODUCT_REPO/...`; split `hil/tools/<product>/` only when a second product arrives |
| 6 | Bench rules (§6) cite bm_cam_legacy CLAUDE.md §15/16 (field-ops) | the rules are copied into SPEC §Hard constraints here, so the citation resolves in this repo |
| 7 | `steps.log` is written by `hil_cmd.sh` / `hil_pistate.sh` but missing from README §3; `hil_p0_probe.sh` cites `hil/procedures/P0_rpicam_probe.md`, which does not exist | applied at the port; README §8 also gained rows for `hil_step.sh` / `hil_change.sh` (added after ec2a449) |
| 8 | No item saying a test must run without an agent | **decided (D8):** HIL-ready item 7, added to `hil/README.md` §1 at the port |
| 9 | `hil/hil.env` is committed in bm_cam_legacy (974c865) although `hil/.gitignore` excludes it; hosts/ids only, no secrets | not copied here (gitignored); tell the Test Engineer after R1, via the EM |

## Decision log

| # | Date | Decision | Rationale |
|---|---|---|---|
| D1 | 2026-10-01 | Keep the Test Engineer's `hil/` as a subfolder, unchanged, with `runs/` at repo root | port = `cp -r hil/ .`; every path in its tools and docs (`hil/tools/…`, `source hil/hil.env`, `runs/`) keeps working |
| D2 | 2026-10-01 | Port after R1 ships (Fri 2026-10-09) or when the Test Engineer calls `hil/` stable, not now | an early snapshot drifts while R1 gates are still editing it; testing is not gated on this repo |
| D3 | 2026-10-01 | Handed-over non-gate tests go in `hil/tests/` | gives dev sessions one place to land a HIL-ready spec; additive, no existing path changes |
| D4 | 2026-10-01 | HIL-ready item 7 "operator-runnable" (proposed) | goal: Nick can run tests without Claude Code; to raise with the Test Engineer after R1 |
| D5 | 2026-10-01 | One home per rule: process in TRACKER, bench rules in SPEC, test format in `hil/README.md`; results only in `runs/*/RESULTS.md` | the template README and DESIGN "Bench / test results" duplicated them |
| D6 | 2026-10-02 | Port (S0) when the Test Engineer reports `hil/` stable, via the EM, even before 10/09 (supersedes D2's "after R1 ships") | Nick, Q1: get the tools here as soon as they stop changing; still one copy, no re-sync |
| D7 | 2026-10-02 | From R2 on, gates are `hil/gates/R<n>_G<m>_<slug>.md` (flat); R1 files keep their names | Nick, Q2: no template change, no file moves at the port |
| D8 | 2026-10-02 | Adopt HIL-ready item 7 "operator-runnable" (D4 proposal) | Nick, Q3: tests must be runnable by Nick without Claude Code |
| D9 | 2026-10-02 | After R1 a standing Test Engineer session owns the bench; Nick approves and can take the bench back at any time | Nick, Q4 |
| D10 | 2026-10-02 | Port = `git archive 7a867d9 hil` (commit A, byte-checked) then only differences #1, #2, #5–#8 (commit B); no tool code changed except one header comment | Test Engineer reported `hil/` stable @ 7a867d9 (EM relay); two commits keep the copy reviewable against its source |

## Run index

*One line per run, newest on top: date · run folder · test · verdict. Results live in the run folder.*

| date | run | test | verdict |
|---|---|---|---|
