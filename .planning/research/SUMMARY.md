# Project Research Summary

**Project:** Email Generation Skill for Publisher Success Managers
**Domain:** Claude Agent Skills / Email Automation
**Researched:** 2026-01-21
**Confidence:** HIGH

## Executive Summary

This project builds a Claude Skill that transforms Gong call transcripts into professional follow-up emails for Postman's Publisher Success team. The approach is refreshingly straightforward: a markdown-based skill using YAML frontmatter and progressive disclosure—no code execution required. This is a "reference content" skill where Claude's text transformation capabilities handle all processing.

The recommended approach uses a single-file MVP (SKILL.md) that grows into a three-component architecture (core instructions, email templates, resource library). The critical technical decision is pure markdown over code execution, which simplifies deployment across Claude Desktop, Claude Code, and API environments. The key risk isn't technical complexity—it's the subtle failure modes unique to Claude Skills: silent activation failures (skill installed but never triggers), cross-platform incompatibility (works in Code but fails in Desktop), and token budget explosion (verbose documentation that degrades performance).

Success requires four non-negotiable elements: (1) a laser-focused skill description with natural trigger phrases, (2) explicit placeholder handling for incomplete transcripts, (3) tone consistency templates with Postman voice guidelines, and (4) dual-platform testing from day one. The research shows that 100+ teams hit the same pitfalls—we can avoid them by building for the lowest common denominator first and treating skill activation as a first-class feature, not an afterthought.

## Key Findings

### Recommended Stack

Claude Skills follow a standardized markdown format that works identically across all platforms. The core architecture is simple: SKILL.md file with YAML frontmatter orchestrates access to supporting resources via progressive disclosure.

**Core technologies:**
- **SKILL.md with YAML frontmatter**: Required entry point, <500 lines optimal — Platform-agnostic, works in Desktop (ZIP), Code (filesystem), API (JSON)
- **Markdown body with references**: Instructions + links to supporting files — Enables selective context loading, reduces token usage
- **Progressive disclosure pattern**: Main instructions + template files + reference library — Load only what's needed per task, scales to complex skills
- **No code execution**: Pure markdown instructions only — Simpler maintenance, easier distribution, leverages Claude's text transformation strength

**Distribution strategy:**
- Claude Desktop/Claude.ai: ZIP upload via Settings → Skills
- Claude Code: Directory in `~/.claude/skills/` or project `.claude/skills/`
- API: Skills parameter with SKILL.md content as JSON

**Key architectural insight:** Same SKILL.md file works everywhere. Platform compatibility is baked into the format, not bolted on later.

### Expected Features

Based on analysis of 14 real user emails and Gong's native capabilities, features break into three tiers:

**Must have (table stakes):**
- Extract action items from transcript — Users expect AI to identify commitments vs. casual mentions
- Extract and hyperlink URLs discussed — Professional emails need clickable links with context
- Generate structured email (greeting, body, next steps, closing) — Consistent format across team
- Maintain human voice/tone — AI-sounding emails damage trust and deliverability
- Save as draft, never auto-send — Fundamental safety requirement
- Extract participant names for greeting — Professional personalization
- Generate subject line following pattern — Action-focused, <60 chars
- Configurable signature — User consistency

**Should have (competitive differentiators):**
- Smart attachment reminder — Detects "deck" or "attached" mentions, prevents common error
- Context-aware resource descriptions — "PayPal example https://..." not bare URLs
- Multi-link formatting optimization — 3+ links become bullet list automatically
- Detect call type (first meeting vs. follow-up) — Adjust tone appropriately

**Defer (v2+):**
- Distinguish discussed vs. committed next steps — Complex NLP, high value but not MVP-critical
- CC participant recognition — Requires deep inference or user input
- Extract meeting cadence suggestions — Nice refinement, not essential
- Suggest follow-up timeline — Helpful but not blocking

**Explicitly exclude (anti-features):**
- Auto-send without review — Violates Claude Skills best practices, damages trust
- Over-personalization — Using name repeatedly feels robotic (2026 spam filters catch this)
- Generic template language — "Hope this finds you well" triggers AI spam detection
- CRM/calendar integration — Scope creep for v1, Gong already handles this
- HTML email design — Plain text is B2B standard, HTML adds complexity
- Tone adjustment slider — Creates decision paralysis, better to learn user's voice

**Critical insight:** Basic text generation is commodity in 2026. Value comes from extraction accuracy, voice consistency, and graceful degradation when transcripts are incomplete.

### Architecture Approach

Claude Skills use progressive disclosure: core SKILL.md orchestrates access to supporting files loaded only when needed. This keeps context efficient while providing comprehensive guidance.

**Recommended file structure:**
```
postman-email-skill/
├── SKILL.md              # Core workflow + orchestration (<500 lines)
├── EMAIL_TEMPLATE.md     # Email format, structure, tone examples
├── RESOURCES.md          # Postman links library (loaded on-demand)
└── CONFIG.md             # User signature, preferences (optional)
```

**Major components:**

1. **SKILL.md (orchestrator)** — Owns workflow logic, high-level instructions, file references, skill metadata. Does NOT own detailed templates or comprehensive link libraries.

2. **EMAIL_TEMPLATE.md (formatter)** — Owns email structure, formatting conventions, tone examples, section requirements. Does NOT own processing logic or resources.

3. **RESOURCES.md (reference library)** — Owns Postman documentation links organized by category, link descriptions, discovery guidance. Does NOT own email formatting or workflow.

4. **CONFIG.md (customization)** — Owns user signature, sender context, tone preferences. Optional until multiple users need customization.

**Information flow:**
```
User uploads transcript
    ↓
Claude recognizes task (description match)
    ↓
Loads SKILL.md into context
    ↓
Follows workflow instructions
    ↓
[Optional] Reads EMAIL_TEMPLATE.md for format
    ↓
[Optional] Reads RESOURCES.md for specific links
    ↓
Generates email following templates
```

**Key patterns:**
- **Progressive disclosure**: Only load what's needed per task (reduces token usage)
- **Template pattern**: Provide structure for Claude to fill in (ensures consistency)
- **Reference library**: Centralize frequently-used info for on-demand access
- **Workflow-driven instructions**: Number steps explicitly, make decision points clear

**Build order recommendation:**
1. **Phase 1: Single-file MVP** — Everything in SKILL.md, fastest path to validation
2. **Phase 2: Extract template** — When template section >100 lines or testing variations
3. **Phase 3: Extract resources** — When links library >50 entries or needs categorization
4. **Phase 4: Add config** — When multiple users or complex preferences emerge

**Anti-patterns to avoid:**
- Deeply nested references (Claude may use `head -100`, missing content)
- Duplicated information across files (wastes tokens, maintenance burden)
- Premature file splitting (adds overhead without benefit)
- Vague file references ("see other file" vs. explicit `[TEMPLATE.md](TEMPLATE.md)`)

### Critical Pitfalls

Based on official docs and analysis of 100+ team failures, five critical pitfalls can derail this project:

1. **Cross-Platform Installation Incompatibility** — Skills built for Claude Code fail when uploaded to Claude Desktop as ZIP. Custom skills don't sync across platforms. Prevention: Design dual-artifact build from start, test in both environments before feature-complete, document installation separately for Code vs. Desktop.

2. **Silent Skill Activation Failures** — Skill appears in list but never auto-triggers. Users must manually invoke with `/skill-name` every time. Why: Vague descriptions don't provide semantic matching context. Prevention: Write activation-focused descriptions with natural trigger phrases users actually say ("Gong transcript", "follow-up email", "call notes"), test with 10+ natural variations, include file type references (".txt transcript").

3. **Token Budget Explosion** — SKILL.md contains everything (docs, examples, edge cases), wastes tokens and "dilutes Claude's attention." Prevention: Keep SKILL.md <500 lines (official recommendation), assume Claude is smart (don't explain concepts it knows), use progressive disclosure pattern, challenge every paragraph's token cost.

4. **Missing Data Graceful Degradation Failure** — Skill crashes or generates placeholder-filled garbage when transcript is incomplete (no customer name, missing next steps). Prevention: Explicit placeholder strategy in SKILL.md (`[CUSTOMER NAME]` with TODO comments), never hallucinate, provide confidence assessment (HIGH/MEDIUM/LOW), include incomplete transcript examples.

5. **Tone Consistency Without Templates** — Each email has different style and formality. One says "Hi John," next says "Dear Mr. Smith," next says "Hey!" Prevention: Explicit tone specification (Postman voice guidelines), template system with variations, examples for every template, style verification checklist.

**Phase-specific warnings:**
- Phase 1 (Infrastructure): Cross-platform incompatibility — Design dual-artifact from start
- Phase 2 (Core Generation): Silent activation + missing data handling — Test triggers early, build placeholders before templates
- Phase 3 (Template System): Token budget + tone consistency — Use progressive disclosure, define voice guidelines with examples
- Phase 4 (Testing): "Works on my machine" syndrome — Fresh installs on both platforms

## Implications for Roadmap

Based on combined research, roadmap should structure into 4 phases with explicit validation gates:

### Phase 1: Single-File MVP
**Rationale:** Fastest path to end-to-end validation. Prove the concept works before investing in architecture. Addresses cross-platform compatibility from day one.

**Delivers:** Working SKILL.md that generates acceptable emails from transcripts in both Claude Code and Claude Desktop.

**Addresses features:**
- Extract action items, participants, URLs
- Generate greeting, subject line, structured body
- Basic tone consistency
- Save as draft

**Avoids pitfall:** Cross-platform incompatibility (test both environments immediately)

**Architecture:** Everything inline in SKILL.md (<300 lines initial)

**Validation gate:** Can Claude generate a good email from a real Gong transcript? Does it work in both Code and Desktop?

**Research flag:** SKIP deeper research — standard Claude Skills pattern, well-documented

---

### Phase 2: Activation Tuning & Placeholder System
**Rationale:** Silent activation is the #1 user complaint. Incomplete transcripts are reality. These must work before adding complexity.

**Delivers:** Skill that auto-triggers reliably + gracefully handles missing data with placeholders.

**Addresses features:**
- Skill description optimized with trigger phrases
- Explicit placeholder strategy for missing names, action items, timelines
- Confidence assessment (HIGH/MEDIUM/LOW)

**Avoids pitfalls:** Silent activation failures, missing data crashes

**Architecture:** Still single SKILL.md, but with activation testing and placeholder examples

**Validation gate:** Does skill auto-trigger on 10+ natural phrase variations? Does it generate useful output from incomplete transcripts without hallucinating?

**Research flag:** SKIP deeper research — activation patterns documented in official best practices

---

### Phase 3: Template System & Voice Guidelines
**Rationale:** Now that activation and core generation work, ensure consistency. Extract templates to separate file when SKILL.md approaches 500 lines.

**Delivers:** Consistent email tone, Postman voice alignment, structured templates.

**Addresses features:**
- EMAIL_TEMPLATE.md with format variations
- Postman voice guidelines (professional but approachable)
- Context-aware resource descriptions
- Smart attachment reminder

**Avoids pitfalls:** Tone inconsistency, token budget explosion

**Architecture:** Split into SKILL.md + EMAIL_TEMPLATE.md (progressive disclosure)

**Validation gate:** Do 5 different transcripts generate similar email style? Does SKILL.md stay <500 lines?

**Research flag:** NEEDS RESEARCH — Postman-specific brand voice guidelines (not in research). Need internal documentation about tone, terminology, formatting preferences.

---

### Phase 4: Resource Library & Polish
**Rationale:** With core functionality solid, add differentiators. Extract resources when link library grows large.

**Delivers:** On-demand Postman documentation links, multi-link formatting, call type detection.

**Addresses features:**
- RESOURCES.md with categorized Postman links
- Multi-link formatting optimization (3+ → bullet list)
- Detect call type (first meeting vs. follow-up)

**Avoids pitfalls:** Deep reference nesting (keep one level from SKILL.md)

**Architecture:** SKILL.md + EMAIL_TEMPLATE.md + RESOURCES.md

**Validation gate:** Does Claude find and include relevant links when appropriate? Does it avoid loading resources unnecessarily?

**Research flag:** SKIP deeper research — reference library pattern well-documented

---

### Phase Ordering Rationale

**Dependencies first:** Core extraction (Phase 1) must work before activation tuning (Phase 2) and templates (Phase 3). Can't test activation if generation doesn't work. Can't assess tone consistency without working generation.

**Risk mitigation:** Address silent activation early (Phase 2) before investing in templates. Build placeholder system before templates to avoid "garbage in, garbage out" with incomplete data.

**Progressive complexity:** Start simple (single file), split when components grow large (500 line guideline). This matches official best practices for skill evolution.

**Validation gates:** Each phase has explicit success criteria. Don't proceed without validation. This prevents cascading failures and wasted effort.

### Research Flags

**Phases needing deeper research during planning:**
- **Phase 3 (Template System):** Postman brand voice guidelines — Need internal documentation about tone, terminology, formatting preferences. This isn't technical research but content research from Postman stakeholders.

**Phases with standard patterns (skip research-phase):**
- **Phase 1 (Single-File MVP):** Standard Claude Skills patterns, official docs comprehensive
- **Phase 2 (Activation Tuning):** Activation mechanics well-documented in best practices
- **Phase 4 (Resource Library):** Reference library pattern established, Postman docs publicly available

**Additional validation needs:**
- **Gong transcript format:** Need sample transcripts to verify structure (speaker labels, timestamps, formatting). Not blocking for Phase 1 but critical for Phase 2 accuracy improvements.
- **Publisher Success workflow:** When are emails sent? Typical response patterns? Escalation criteria? Nice-to-have context but not blocking.

## Confidence Assessment

| Area | Confidence | Notes |
|------|------------|-------|
| Stack | HIGH | SKILL.md format official standard, verified across docs |
| Features | HIGH | Based on 14 real user emails + Gong official docs + Claude best practices |
| Architecture | HIGH | Progressive disclosure core design principle, official repository examples |
| Pitfalls | HIGH | Verified with official docs + community troubleshooting + 100+ team failure analysis |

**Overall confidence:** HIGH

All core recommendations backed by official Anthropic documentation. Feature priorities validated against real user emails. Pitfalls verified across multiple authoritative sources.

### Gaps to Address

**Minor gaps (don't block initial development):**

1. **Postman brand voice specifics** — Need internal guidelines for tone, terminology, banned phrases.
   - **Handling:** Phase 3 planning requires stakeholder interview or doc review
   - **Impact:** Affects template examples but not core architecture

2. **Gong transcript format details** — Need to verify actual structure (metadata format, speaker labels, timestamp conventions).
   - **Handling:** Get 2-3 sample transcripts during Phase 1, adjust parsing in Phase 2
   - **Impact:** May need prompt tuning for extraction accuracy

3. **Publisher Success team workflow** — Understanding when emails sent, typical patterns, escalation paths.
   - **Handling:** Nice-to-have context, gather during rollout feedback
   - **Impact:** Informs Phase 4 polish features but not MVP

4. **Multi-user deployment patterns** — How will multiple PS managers share/maintain skill?
   - **Handling:** Defer CONFIG.md extraction until team feedback in Phase 4
   - **Impact:** May need Git workflow documentation, not architecture change

**No blocking gaps identified.** Research is sufficient to begin Phase 1 implementation.

## Sources

### Primary (HIGH confidence)
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills) — File structure, YAML frontmatter, best practices
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) — Conciseness principles, description writing, progressive disclosure
- [Agent Skills Overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview) — How skills work, architecture design
- [Generate a follow-up email - Gong Help](https://help.gong.io/docs/generate-a-follow-up-email) — Gong native capabilities
- Project file: `follow_up_emails_agentic_context.md` — 14 real user emails analyzed

### Secondary (MEDIUM confidence)
- [Claude Skills Deep Dive by Lee Han Chung](https://leehanchung.github.io/blogs/2025/10/26/claude-skills-deep-dive/) — Technical architecture analysis
- [100+ People Hit the Same Claude Skills Problems](https://natesnewsletter.substack.com/p/i-watched-100-people-hit-the-same) — Common failure modes
- [Inside Claude Code Skills by Mikhail Shilkov](https://mikhail.io/2025/10/claude-code-skills/) — Invocation mechanics
- [Best AI email assistant in 2026 - Superhuman](https://blog.superhuman.com/best-ai-email-assistant/) — 2026 email automation landscape

### Tertiary (LOW confidence)
- [Claude Code Email Productivity - Harper Reed](https://harper.blog/2025/12/03/claude-code-email-productivity-mcp-agents/) — Real-world usage patterns
- [Scientific Tips for Writing Follow-Up Emails - Gong](https://www.gong.io/blog/7-tips-for-writing-the-perfect-follow-up-sales-email-according-to-science) — Email best practices

**Verification notes:**
- All architectural patterns cross-verified against official Anthropic documentation
- Progressive disclosure confirmed as core design principle in engineering blog
- Pitfalls validated across official troubleshooting + community failure analysis
- Feature priorities verified against real user email corpus

---
*Research completed: 2026-01-21*
*Ready for roadmap: yes*
