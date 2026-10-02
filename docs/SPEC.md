# SPEC.md — nereus_hil_testing

*What Nick wants. Stable reference — agents skim this; changes require Nick's approval.*
*Last updated: 2026-10-02*

## Goal

One home for Nereus hardware-in-the-loop (HIL) testing, in one format for every project.
Done when Nick can take any HIL-ready test from this repo, run it on the bench from the
copy-pastable commands alone (no Claude Code session needed), and get a run folder whose
`RESULTS.md` says PASS or FAIL per criterion with the evidence file next to it.

## Background

- Release R1 (ship Fri 2026-10-09) has a Test Engineer session in `bm_cam_legacy` that built
  a self-contained `hil/` folder (format README, gates, procedures, tools, templates) meant to
  be copied here. Source: bm_cam_legacy `origin/feature/r1-hil-test-engineer` @ ec2a449.
- Conventions reused from bm_cam_legacy: "trust artifacts, not exit codes"; self-contained run
  folders with `run_manifest.json` + `RESULTS.md` (CLAUDE.md §10); field-ops and reversible
  changes (CLAUDE.md §15/16); the SPEC / DESIGN / TRACKER / DEV_LOG pattern
  (`sprints/cc_prompts/CC_PROMPT_sprint_worker.md`); one PASS/FAIL row per criterion (`runs/*/RESULTS.md`).
- Process docs come from `template_LLM_project` (Nick's agent-process template).

## Inventory / environment

Source: bm_cam_legacy `hil/hil.env.example` and `hil/README.md` §6 @ ec2a449. Verify before use.

| Item | Qty | Role |
|---|---|---|
| nereus000 (`pi@192.168.1.45`) | 1 | console monitor host (spotter-monitor, `cmd.txt` lane); holds the staging admin token |
| Bench rig A: SPOT-33507C / BMCAM_003 / bmcam003 | 1 | R1 primary bench unit |
| Bench rig B: SPOT-31593C / BMCAM_004 / bmcam004 | 1 | R1 second bench unit |
| Field units: SPOT-33361C, bmcam001, bmcam002 | 3 | **never** a HIL target (constraint 3) |
| Staging API `https://nereus-vision-staging.onrender.com` | 1 | used from nereus000 only |

## Safety / hard constraints (non-negotiable)

1. **Desk sessions never touch hardware.** Only the bench owner runs anything against
   nereus000, a Spotter or a unit. Everyone else asks the owner first.
2. **One bench owner at a time**, recorded in the run's `gate.log`. After R1 the owner is a
   standing Test Engineer session; Nick can take the bench back at any time (DESIGN D9).
3. **Never send to a field Spotter or unit** (SPOT-33361C, bmcam001, bmcam002) from a HIL session.
4. **Field-ops (from bm_cam_legacy CLAUDE.md §15/16):** check for running camera processes;
   back up crontab and config before any change and restore them after; write the restore
   command before running the change; never leave a unit disabled, disarmed or mid-surgery;
   avoid reboot loops; prefer reversible changes; record every boot / cron / config change in `gate.log`.
5. **No secrets in git.** Tokens stay on the monitor host; `hil/hil.env` is gitignored.
6. **Nick decides first** (via the EM during a release): cellular spend beyond a test's
   budget, anything Nick must physically touch, any Render / backend change.
7. **Evidence is files, not exit codes.** A criterion's verdict cites a file in its run folder.

Test-format bench rules (console line limits, heal sender, ssh/IPC gotchas) live in
`hil/README.md` §6.

## Success criteria by sprint

See TRACKER.md — every sprint ends with a live demo Nick can run.

## Non-goals (this project)

- Product code. Code under test lives in its product repo; tests reference a sha.
- Moving past run evidence (R1 runs stay in bm_cam_legacy `runs/`).
- CI or automated scheduling of bench runs (later, if ever).

## Open questions (flag, don't guess)

None open.

Answered (Nick 2026-10-02):
- Q1. Port timing → when the Test Engineer reports `hil/` stable (via the EM), even before 10/09 (DESIGN D6).
- Q2. Gate naming from R2 on → flat, release prefix: `hil/gates/R2_G1_<slug>.md` (D7).
- Q3. HIL-ready item 7 "operator-runnable" → adopted (D8).
- Q4. Bench owner after R1 → a standing Test Engineer session (D9).
