# Email Assistant Skill — Review Findings

**Reviewed:** 2026-02-05
**Method:** Codex MCP (read-only sandbox) + manual exploration
**Files reviewed:** 8 (SKILL.md, README.md, VOICE.md, 3 modes, 2 resources)

---

## Summary

9 issues found across 3 priority levels. The skill is architecturally sound — clean orchestrator/mode/resource separation. The main gaps are: a phantom file reference in README, missing voice integration in GONG_FOLLOWUP, and thin EXTERNAL/OUTREACH modes that lack examples and guardrails compared to the mature GONG_FOLLOWUP mode.

---

## Findings

### #1 — HIGH: README references missing `PROFILE.md`

**Files:** `README.md`
**Issue:** The file tree in README lists `PROFILE.md` as a standalone file, but it doesn't exist. User context was inlined into `SKILL.md` during development.
**Fix:** Update README file tree to remove `PROFILE.md` and add a note that user context lives in `SKILL.md`.

---

### #2 — HIGH: Mode detection criteria inconsistent between SKILL.md and GONG_FOLLOWUP.md

**Files:** `SKILL.md`, `MODES/GONG_FOLLOWUP.md`
**Issue:** `SKILL.md` says "Transcript/meeting notes provided" triggers Gong mode. `GONG_FOLLOWUP.md` says it activates on "transcript with any email-related intent." This creates ambiguous routing — does pasting a transcript alone trigger it, or is intent required?
**Fix:** Align both files. Recommend: transcript present = auto-detect Gong mode (matching SKILL.md), and GONG_FOLLOWUP.md's existing "ask if intent unclear" protocol handles edge cases.

---

### #3 — HIGH: GONG_FOLLOWUP.md lacks VOICE.md reference

**Files:** `MODES/GONG_FOLLOWUP.md`
**Issue:** Unlike EXTERNAL.md and OUTREACH.md, GONG_FOLLOWUP.md never references VOICE.md. While its examples match David's voice, there's no explicit instruction to apply voice patterns, risking drift.
**Fix:** Add a `## Voice` section referencing VOICE.md's External Professional patterns (formality 6-7/10), consistent with the other modes.

---

### #4 — MEDIUM: Keyword mismatches in outreach triggers

**Files:** `SKILL.md`, `MODES/OUTREACH.md`
**Issue:** SKILL.md lists keywords: "cold", "outreach", "intro email", "first contact", "re-engage". OUTREACH.md lists: "cold outreach", "intro email", "re-engage", "reconnect". Mismatches: "first contact" missing from OUTREACH.md; "reconnect" missing from SKILL.md; "outreach" vs "cold outreach" granularity differs.
**Fix:** Sync both files to a single canonical keyword list: "cold outreach", "intro email", "first contact", "re-engage", "reconnect".

---

### #5 — MEDIUM: EXTERNAL.md is skeletal (30 lines, 0 examples)

**Files:** `MODES/EXTERNAL.md`
**Issue:** This is the default mode but has no examples, no guardrails, no clarification protocol, and no anti-hallucination rules. Compare to GONG_FOLLOWUP.md's 228 lines with 3 examples and detailed protocols.
**Fix:** Add 1 example email, basic guardrails (don't invent facts, use placeholders for unknowns), and a brief clarification note. Keep concise — this mode is more freeform than Gong, so ~60-80 lines total is appropriate.

---

### #6 — MEDIUM: OUTREACH.md is skeletal (32 lines, 0 examples)

**Files:** `MODES/OUTREACH.md`
**Issue:** Same as #5. No examples despite VOICE.md containing two excellent outreach samples (B1, B2) that could be adapted.
**Fix:** Add 1 example email (adapted from VOICE.md samples), basic guardrails, and a note. Target ~60-80 lines.

---

### #7 — MEDIUM: Internal Team voice exists but no INTERNAL mode

**Files:** `VOICE.md`, `SKILL.md`
**Issue:** VOICE.md documents "Internal Team" as a category (formality 4-5/10, 2 samples) but SKILL.md has no routing for internal emails and no INTERNAL mode file exists.
**Fix:** Add a note in SKILL.md acknowledging internal voice data exists in VOICE.md and that internal emails can use the External mode with lower formality. A full INTERNAL.md mode is not needed at this stage — the voice data is sufficient for Claude to adapt.

---

### #8 — LOW: Manual override tokens unspecified

**Files:** `SKILL.md`
**Issue:** "User says 'use [mode] mode'" doesn't list accepted tokens. Claude may interpret "use professional mode" differently than intended.
**Fix:** Add accepted override tokens: `gong`, `external`, `outreach` (case-insensitive).

---

### #9 — LOW: Truncated URLs in GONG_FOLLOWUP examples

**Files:** `MODES/GONG_FOLLOWUP.md`
**Issue:** Example links appear cut off (e.g., `.../working-with-multi-partner-opportunit`). These are in examples only but train incorrect patterns.
**Fix:** Replace with full valid URLs or use clearly-marked placeholder URLs.

---

### #10 — LOW: Self-referential note in VOICE.md line 213

**Files:** `VOICE.md`
**Issue:** Line 213 says "copy into VOICE.md for quick reference" — but this IS VOICE.md. Leftover from extraction process.
**Fix:** Change heading to "Quick Writing Recipe" (remove the self-referential instruction).

---

### #11 — LOW: Inconsistent path styles across files

**Files:** Various
**Issue:** GONG_FOLLOWUP.md uses `../RESOURCES/POSTMAN.md` (relative from MODES/). SKILL.md uses `RESOURCES/POSTMAN.md` (relative from root). EXTERNAL.md uses `../RESOURCES/POSTMAN.md`. Not broken, but inconsistent.
**Fix:** Standardize all references to relative-from-skill-root paths (e.g., `RESOURCES/POSTMAN.md`, `MODES/GONG_FOLLOWUP.md`).

---

### #12 — LOW: TEMPLATE.md claims "automatic" matching

**Files:** `RESOURCES/TEMPLATE.md`
**Issue:** States "the skill will automatically match conversation topics to resources" — but matching is instructive guidance, not programmatic automation.
**Fix:** Rephrase to clarify the matching is done by Claude following the keyword hints, not by an automated system.

---

## Fix Implementation Plan

| Commit | Priority | Issues | Description |
|--------|----------|--------|-------------|
| 1 | HIGH | #1 | Update README to reflect inlined profile |
| 2 | HIGH | #2, #3 | Add VOICE.md reference to GONG_FOLLOWUP + align mode detection |
| 3 | MEDIUM | #5 | Flesh out EXTERNAL mode with example and guardrails |
| 4 | MEDIUM | #6 | Flesh out OUTREACH mode with example and guardrails |
| 5 | MEDIUM/LOW | #4, #7, #8, #9, #10, #11, #12 | Sync keywords, paths, cleanup across files |

**Git strategy:** Feature branch `fix/review-findings` with atomic commits, PR to main for review before merge.
