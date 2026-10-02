# CLAUDE.md — nereus_hil_testing

## Start here, every session

This repo runs on the agent discipline in **docs/TRACKER.md**. Before any other
work: run **/agent-entry** (or follow the Rules for Agents at the top of
docs/TRACKER.md). Owner and approval gate: **Nick**.

Docs map — read per the ritual, don't skip it:

- `docs/SPEC.md` — goal; verified facts; hard constraints; open questions
- `docs/TRACKER.md` — rules + sprint ladder (the entry point)
- `docs/DESIGN.md` — as-built architecture + decision log
- `docs/DEV_LOG.md` — session log, newest first
- `docs/PROMPTS.md` — Nick's kickoff prompts

Layout: `hil/` = HIL format, gates, tests, tools, templates · `runs/` = one evidence
folder per run (RESULTS.md + run_manifest.json). Details: docs/DESIGN.md §Repo layout.

## Engineering values (apply to every bite)

1. **Boring, debuggable engineering.** Small modules, explicit control flow,
   visible logs, plain formats. Build for the current sprint, not an imagined
   future.
2. **Reuse before rewriting.** Inspect prior art first; adapt the smallest
   working piece; document what was reused. A rewrite needs a measurable reason.
3. **Never invent facts.** APIs, formats, limits, pinouts: verify against
   primary sources or measure, else flag in SPEC.md §Open questions.
4. **Trust artifacts, not exit codes.** Verify outputs exist, sizes are
   plausible, and content is usable before calling anything done.
5. **One variable at a time.** Record the known-good path before changing it.
6. **Fail loudly and usefully.** Errors carry context and a recovery hint;
   partial failure never destroys good data.
7. **Desk work never touches hardware.** Only the named bench owner (or Nick) runs
   anything against nereus000, a Spotter or a unit — docs/SPEC.md §Safety.
8. **Hard constraints in SPEC.md are absolute.**

> Never trust a script just because it exits successfully. Trust the artifacts.
