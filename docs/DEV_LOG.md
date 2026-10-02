# DEV_LOG.md — Session Log

*Newest entries on top. One entry per working session. Short: what changed,
what broke, what's next.*

---

## Entry template

```
## YYYY-MM-DD — Sprint Sn — <one-line summary>
**Branch:** sprint/n-slug
**Done:**  <bullets>
**Broke/surprised us:** <bullets or "nothing">
**Next:** <the single next bite>
```

---

## 2026-10-02 — Pre-work — SPEC Q1–Q4 answered by Nick

**Branch:** sprint/0-open-questions
**Done:** recorded Nick's answers as DESIGN D6–D9: port when the TE reports `hil/` stable
(via the EM); R2+ gates `R<n>_G<m>_<slug>.md` flat; HIL-ready item 7 adopted; a standing
Test Engineer session owns the bench after R1. SPEC constraints 1–2 and TRACKER S0/S1
updated. Desk work only.
**Broke/surprised us:** nothing.
**Next:** S0 plan nibble when the EM relays the TE's "hil/ stable" — Nick gate.

---

## 2026-10-01 — Pre-work — Templates cleaned up for HIL; layout + HIL-ready checklist proposed

**Branch:** sprint/0-repo-setup (initial commit on main = templates verbatim, Nick-approved)
**Done:** placeholders filled; restored the missing `.claude/skills/` (agent-entry,
capture-task) from template_LLM_project; README rewritten for this repo; SPEC filled
(goal, bench inventory, hard constraints, open questions); TRACKER S0 (port) + S1
(first handed-over test); DESIGN: layout, HIL-ready checklist, 8 differences vs the
Test Engineer's `hil/` (bm_cam_legacy ec2a449), decisions D1–D5. Desk work only.
**Broke/surprised us:** the template copy dropped `.claude/skills/` (template README
warns about this); template README and DESIGN duplicated TRACKER rules / run results.
**Next:** S0 plan nibble once SPEC Q1 (port timing) is answered — Nick gate.
