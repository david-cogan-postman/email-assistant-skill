# Roadmap: Publisher Follow-up Email Skill

## Overview

This roadmap delivers a Claude skill that transforms Gong transcripts into professional follow-up emails for Postman's Publisher Success team. The journey progresses from a working single-file skill (Phase 1) through robust handling of real-world edge cases (Phase 2) to team-ready distribution (Phase 3). The entire v1 scope delivers in 3 phases optimized for quick validation and iteration.

## Phases

**Phase Numbering:**
- Integer phases (1, 2, 3): Planned milestone work
- Decimal phases (2.1, 2.2): Urgent insertions (marked with INSERTED)

Decimal phases appear between their surrounding integers in numeric order.

- [x] **Phase 1: Single-File MVP** - Core extraction and email generation in working SKILL.md
- [ ] **Phase 2: Robust Handling** - Activation tuning, placeholders, and Postman resource library
- [ ] **Phase 3: Distribution** - Platform packaging and team setup documentation

## Phase Details

### Phase 1: Single-File MVP
**Goal**: User can paste a Gong transcript and receive a professional, ready-to-send follow-up email

**Depends on**: Nothing (first phase)

**Requirements**: EXTR-01, EXTR-02, EXTR-03, EXTR-04, GEN-01, GEN-02, GEN-03, GEN-04, GEN-05, CONF-01, CONF-03, DLVR-01, DLVR-02

**Success Criteria** (what must be TRUE):
  1. User can paste Gong transcript into Claude and receive a structured follow-up email
  2. Email includes extracted participant names, action items, and resources from transcript
  3. Resources appear as hyperlinked text (e.g., "verification checklist") not raw URLs
  4. Email uses configurable signature (default: "Best,\nDavid")
  5. Missing information shows clear [PLACEHOLDER] markers instead of hallucinated content

**Plans:** 1 plan

Plans:
- [x] 01-01-PLAN.md — Create complete SKILL.md with extraction, generation, and few-shot examples

### Phase 2: Robust Handling
**Goal**: Skill handles incomplete transcripts gracefully and suggests relevant Postman resources contextually

**Depends on**: Phase 1

**Requirements**: CONF-02

**Success Criteria** (what must be TRUE):
  1. Skill auto-activates reliably when user pastes transcript (no manual /skill-name invocation needed)
  2. Skill generates useful email even when transcript is incomplete (missing names, partial action items)
  3. Skill suggests relevant Postman documentation links based on call context (verification, notebooks, Run in Postman, etc.)
  4. Attachment reminder appears when transcript mentions "deck", "presentation", "PDF", or "attached"

**Plans**: TBD (during phase planning)

Plans:
- [ ] 02-01: TBD
- [ ] 02-02: TBD

### Phase 3: Distribution
**Goal**: Team members can install and use the skill in their preferred Claude environment

**Depends on**: Phase 2

**Requirements**: PLAT-01, PLAT-02, PLAT-03, DIST-01, DIST-02

**Success Criteria** (what must be TRUE):
  1. Identical SKILL.md works in both Claude Desktop (ZIP upload) and Claude Code (~/.claude/skills/)
  2. Non-technical team members can install skill following setup instructions
  3. ZIP package includes all necessary files and clear installation steps
  4. Team members can customize signature without editing skill logic

**Plans**: TBD (during phase planning)

Plans:
- [ ] 03-01: TBD

## Progress

**Execution Order:**
Phases execute in numeric order: 1 -> 2 -> 3

| Phase | Plans Complete | Status | Completed |
|-------|----------------|--------|-----------|
| 1. Single-File MVP | 1/1 | Complete | 2026-01-21 |
| 2. Robust Handling | 0/2 | Not started | - |
| 3. Distribution | 0/1 | Not started | - |

---
*Roadmap created: 2026-01-21*
*Last updated: 2026-01-21*
