# TRACKER.md — Sprint Ladder & Rules

*The agent entry point. Newest state lives here.*
*Last updated: 2026-10-02 · Owner/gate: **Nick***

---

## Rules for Agents (READ FIRST, EVERY SESSION)

1. **Read this whole document cover-to-cover first, every session.** Then skim
   `docs/SPEC.md` and `docs/DESIGN.md`. Read the top ~3 entries of
   `docs/DEV_LOG.md`.
2. **Take small code bites.** One TODO at a time, target ~300 LoC. If SPEC.md
   is too thin to inform the bite, stop and ask Nick.
3. **Four nibbles per bite:**
   1. **Plan** — throwaway code ok; change no files. *Gate: Nick approves.*
   2. **Code + unit tests** — flag Nick on substantial plan changes.
   3. **Manual tests** — Nick runs them; provide copy-pastable CLI.
   4. **Open PR.**
4. **Feature branch for all new work** — `sprint/<n>-<slug>`, PR to `main`. Never
   commit to main. (Only exception: the initial commit of 2026-10-01, Nick-approved.)
5. **Every sprint ends with a live demo Nick can run** — exact commands in
   the sprint's Demo section and the PR description.
6. **End of every session:** DEV_LOG.md entry (newest on top); DESIGN.md updated
   on any architecture/decision change.
7. **Facts carry sources; unknowns get flagged, not guessed.**
8. **Hardware: SPEC §Safety is absolute.** Stop and report instead of proceeding when a
   step needs the bench and you are not the named bench owner, or would spend cellular,
   touch a crontab, halt a unit or change deployed device state without Nick's go-ahead.
   A test is checked off only when its run folder's RESULTS.md proves it.

### Project layout

```
docs/     SPEC.md TRACKER.md DESIGN.md DEV_LOG.md PROMPTS.md
hil/      the HIL format, gates, tests, procedures, tools, templates (DESIGN §Repo layout)
runs/     one folder per run: <test>_<YYYYMMDD>/ with RESULTS.md + run_manifest.json
```

---

## Sprint ladder

State key: `[ ]` pending · `[~]` in progress · `[x]` done · `[!]` blocked

### S0 — Port the R1 `hil/` format  `[~]`  (demo pending: Nick)
**Goal:** this repo holds the Test Engineer's `hil/` as a copy, with only the DESIGN
differences #1, #2, #5–#8 applied.
- [x] `git -C ../bm_cam_legacy fetch`; copy `hil/` from the agreed sha (7a867d9); record the sha in DEV_LOG
- [x] apply differences #1, #2 (`.gitignore`), #5 (`HIL_PRODUCT_REPO`), #6, #7, #8 (item 7 into `hil/README.md` §1); nothing else
- [x] remove the "until the port" pointer text from DESIGN.md
**Demo (Nick):** `hil/tools/hil_new_run.sh demo_port DEMO` → prints `runs/demo_port_<date>/`;
that folder has RESULTS.md, run_manifest.json (valid JSON, schema `hil_run_manifest/1`),
commands.log, gate.log and the five subfolders. Desk-only: no unit is contacted.
**Needs:** the Test Engineer reports `hil/` stable, relayed by the EM (DESIGN D6). Do not ask the TE directly during R1.

### S1 — First handed-over test, run by Nick alone  `[ ]`
**Goal:** one non-gate test lands in `hil/tests/` HIL-ready (all 7 items) and Nick runs it.
- [ ] pick the test with Nick
- [ ] spec passes the HIL-ready checklist review (missing rows named, then fixed)
**Demo (Nick):** Nick runs the test from its spec with no agent in the loop; the run folder's
RESULTS.md has a verdict per criterion and every evidence file it names exists.
**Needs:** S0; a bench window from the bench owner (standing Test Engineer session, D9).

---

## Icebox (captured, not scheduled)

- `hil_finish_run.py` (named in hil/README §5, not built yet): fill `end_*`, `units[].after`, verdict.
- A `hil_ready_check.py` that lints a spec for the 7 HIL-ready items.
