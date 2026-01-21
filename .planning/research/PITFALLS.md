# Pitfalls Research: Claude Skill Development
# Email Generation from Gong Transcripts

**Domain:** Claude skill for email generation
**Project:** Email follow-up generation for Publisher Success managers at Postman
**Researched:** 2026-01-21
**Overall confidence:** HIGH (verified with official documentation and 2025-2026 sources)

---

## Executive Summary

Claude skill development for email generation has **five critical failure modes** that can derail cross-platform deployment. The highest-risk pitfall is **skill portability blindness**—building for one platform (Claude Code) without considering others (Claude Desktop ZIP). The second most dangerous is **silent skill activation failures**, where skills appear installed but never trigger. These pitfalls are domain-specific: transcript processing requires explicit error handling for missing data, while professional email generation demands strict tone consistency mechanisms that generic skills don't need.

**Impact on this project:** Your dual-platform requirement (Claude Code + Claude Desktop) makes you vulnerable to platform-specific configuration errors. Your use case (Gong transcript → email) requires graceful degradation when transcripts are incomplete, which most skills ignore.

---

## Critical Pitfalls

### Pitfall 1: Cross-Platform Installation Incompatibility

**What goes wrong:** Skills built for Claude Code (~/.claude/skills/) fail when uploaded to Claude Desktop (ZIP), or vice versa. Teams assume skills "just work" across platforms but discover deployment failures at go-live.

**Why it happens:**
- Claude Code uses filesystem-based discovery (`~/.claude/skills/<skill-name>/SKILL.md`)
- Claude Desktop/Web require ZIP upload through Settings UI
- **Custom skills do NOT sync across platforms** (explicitly documented)
- Skills uploaded to claude.ai must be separately uploaded to API
- Different runtime environments (Claude Code has network access, API doesn't)

**Consequences:**
- Development cycle: Build for Code → Repackage for Desktop → Discover compatibility issues → Rebuild
- Team installation friction: "It works on my machine" but not theirs
- Maintenance burden: Two deployment paths for every update

**Prevention:**

1. **Design for lowest-common-denominator first:**
   - Start with features that work in ALL environments
   - Document network-dependent features separately
   - Test in BOTH environments before feature-complete

2. **Create dual-artifact build process:**
   ```bash
   # Development (Claude Code)
   ~/.claude/skills/email-from-gong/SKILL.md

   # Production (Claude Desktop)
   email-from-gong.zip
   ├── SKILL.md
   ├── templates/
   └── examples/
   ```

3. **Explicit installation documentation:**
   - Separate README sections for Code vs Desktop
   - Include screenshots for ZIP upload process
   - Provide verification steps for each platform

**Detection warning signs:**
- Skill works locally but teammates can't use it
- Different behavior between Code and Desktop
- "Skill not found" errors on fresh installs

**Phase impact:** Address in **Phase 1: Core Infrastructure**. Waiting creates technical debt.

**Sources:**
- [Claude Skills Overview](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)
- [Using Skills in Claude Help Center](https://support.claude.com/en/articles/12512180-using-skills-in-claude)
- [The Definitive Guide to Claude SKILLS](https://limitededitionjonathan.substack.com/p/the-definitive-guide-to-claude-skills)

---

### Pitfall 2: Silent Skill Activation Failures

**What goes wrong:** Skills appear in the skills list but Claude never automatically invokes them. Users type requests that should trigger the skill, but Claude responds with generic capabilities instead. This is the #1 user complaint.

**Why it happens:**
- **Skill discovery depends on semantic matching**, not exact keywords
- Vague descriptions don't provide enough trigger context
- Description doesn't include natural language users would actually say
- Character budget limits exclude skill descriptions from context (default: 15,000 chars)
- Over-general descriptions cause skill to match too broadly, so Claude becomes uncertain

**Consequences:**
- Users must manually invoke with `/skill-name` every time
- Skill becomes documentation rather than automation
- Team loses confidence in skill system

**Prevention:**

1. **Write activation-focused descriptions:**

   **Bad (vague):**
   ```yaml
   description: Helps with email generation
   ```

   **Good (specific with triggers):**
   ```yaml
   description: Generates professional follow-up emails from Gong call transcripts. Use when user mentions "Gong transcript", "follow-up email", "call notes", "meeting summary", or uploads a transcript file. Includes placeholder handling for missing information.
   ```

2. **Test activation explicitly:**
   ```bash
   # Verify skill appears
   "What skills are available?"

   # Test natural trigger phrases
   "I have a Gong transcript, help me write a follow-up email"
   "Generate email from this call transcript"
   "Create follow-up email for customer meeting"

   # Should auto-invoke, not require /email-from-gong
   ```

3. **Increase character budget if needed:**
   ```bash
   export SLASH_COMMAND_TOOL_CHAR_BUDGET=20000
   ```

4. **Use keywords users actually say:**
   - Survey team: "How would you ask for this?"
   - Include synonyms: "transcript", "call notes", "meeting recording"
   - Reference file types: ".txt transcript", "Gong export"

**Detection warning signs:**
- Skill shows in list but never auto-triggers
- Users consistently need to use `/skill-name`
- "Can you help me with X?" → generic response instead of skill activation

**Phase impact:** Must be verified in **Phase 2: Core Email Generation** before declaring MVP complete.

**Sources:**
- [Claude Code Skills Best Practices](https://code.claude.com/docs/en/skills)
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [I Watched 100+ People Hit the Same Claude Skills Problems](https://natesnewsletter.substack.com/p/i-watched-100-people-hit-the-same)

---

### Pitfall 3: Token Budget Explosion

**What goes wrong:** SKILL.md contains everything—full documentation, examples, error handling, edge cases. This wastes tokens and "dilutes Claude's attention regarding core instructions." Ironically, comprehensive documentation makes skills LESS effective.

**Why it happens:**
- Natural instinct: "More documentation = better results"
- Fear of missing edge cases drives over-specification
- Copying patterns from traditional documentation
- Not understanding progressive disclosure architecture

**Consequences:**
- Every skill invocation loads unnecessary context
- Critical instructions buried in verbose explanations
- Context window fills faster, reducing conversation history
- Claude may miss important details in the noise

**Prevention:**

1. **Keep SKILL.md under 500 lines** (official recommendation)

2. **Assume Claude is smart:**
   ```markdown
   ## Bad (150 tokens)
   PDF (Portable Document Format) files are a common file format
   that contains text, images, and other content. To extract text
   from a PDF, you'll need to use a library...

   ## Good (50 tokens)
   Extract text with pdfplumber:
   ```python
   import pdfplumber
   with pdfplumber.open("file.pdf") as pdf:
       text = pdf.pages[0].extract_text()
   ```
   ```

3. **Use progressive disclosure pattern:**
   ```
   email-from-gong/
   ├── SKILL.md              # Overview + core instructions (<500 lines)
   ├── TEMPLATES.md          # Email templates (loaded when needed)
   ├── EXAMPLES.md           # Example transcripts + outputs
   └── TROUBLESHOOTING.md    # Edge case handling
   ```

   In SKILL.md:
   ```markdown
   ## Email templates
   See [TEMPLATES.md](TEMPLATES.md) for formal, casual, and technical styles.

   ## Handling edge cases
   See [TROUBLESHOOTING.md](TROUBLESHOOTING.md) for incomplete transcripts.
   ```

4. **Challenge every paragraph:**
   - "Does Claude really need this explanation?"
   - "Can I assume Claude knows this?"
   - "Does this justify its token cost?"

**Detection warning signs:**
- SKILL.md exceeds 500 lines
- Explanations like "A follow-up email is a message sent after..."
- Multiple paragraphs explaining concepts Claude already knows
- Same instructions repeated in different sections

**Phase impact:** Critical for **Phase 3: Template System**. If SKILL.md already large, adding templates will exceed budget.

**Sources:**
- [Skill Authoring Best Practices - Concise is Key](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices#concise-is-key)
- [Claude Skills: Progressive Disclosure Patterns](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices#progressive-disclosure-patterns)

---

### Pitfall 4: Missing Data Graceful Degradation Failure

**What goes wrong:** Skill fails or generates placeholder-filled garbage when transcript is incomplete. Gong transcripts often lack customer names, next steps, or specific commitments. Skills that don't anticipate this produce emails like "Dear [NAME]" or crash with validation errors.

**Why it happens:**
- Skills assume perfect input data
- No explicit instructions for missing information
- Validation scripts reject incomplete data instead of degrading gracefully
- No examples showing partial transcript handling

**Consequences:**
- User frustration: "Skill only works with perfect inputs"
- Manual fallback: User must edit transcript before using skill
- Reduced adoption: "Easier to write email myself"
- Professional risk: Accidentally sending placeholder-filled emails

**Prevention:**

1. **Explicit placeholder strategy in SKILL.md:**
   ```markdown
   ## Handling missing information

   When transcript lacks specific information:

   1. **Critical fields** (customer name, company):
      - Use `[CUSTOMER NAME]` placeholder
      - Add comment: `<!-- TODO: Add customer name -->`

   2. **Optional fields** (next steps, timeline):
      - Omit section entirely
      - Don't write "we will [NEXT STEPS]"

   3. **Ambiguous information**:
      - Use general language: "following our discussion" not "as you mentioned"
      - Avoid specifics that might be wrong

   4. **Never hallucinate**:
      - Don't infer names from email addresses
      - Don't guess timelines or commitments
      - If unsure, use placeholder + comment
   ```

2. **Provide validation without rejection:**
   ```markdown
   ## Review checklist

   After generating email:
   1. Search for `[` placeholder markers
   2. List all placeholders at bottom:
      - `<!-- PLACEHOLDERS: [CUSTOMER NAME], [TIMELINE] -->`
   3. Provide confidence assessment:
      - HIGH: All critical info present
      - MEDIUM: Minor placeholders (salutation, title)
      - LOW: Major placeholders (name, next steps)
   ```

3. **Include incomplete transcript examples:**
   ```markdown
   ## Example 3: Incomplete transcript

   **Input transcript:**
   ```
   Discussion about API rate limits. Customer asked about
   enterprise plan. Mentioned they need higher limits.
   [Audio quality poor from 15:30-18:00]
   ```

   **Generated email:**
   ```
   Subject: Following up on API rate limits discussion

   Hi [CUSTOMER NAME],

   Thanks for discussing your API needs today. Based on our
   conversation about rate limits and your interest in the
   enterprise plan, I wanted to follow up.

   <!-- TODO: Add specific rate limit requirements mentioned -->
   <!-- TODO: Confirm timeline if discussed -->

   Let me know if you'd like to schedule a follow-up call to
   discuss your specific requirements in detail.
   ```
   ```

4. **Fail gracefully, not silently:**
   ```markdown
   ## Validation workflow

   1. Generate email from transcript
   2. Count placeholders
   3. **If >3 critical placeholders:**
      - Display email + warning
      - "⚠️ This transcript is incomplete. Email requires
         significant manual editing before sending."
   4. **Otherwise:**
      - Display email + placeholder list
      - Ready for review
   ```

**Detection warning signs:**
- Generated emails contain `[PLACEHOLDER]` or `null` values
- Emails reference details not in transcript
- Users complain: "Skill doesn't work with real transcripts"
- Validation fails on 50%+ of real inputs

**Phase impact:** Must be addressed in **Phase 2: Core Email Generation**. Don't defer to polish phase.

**Sources:**
- [Claude Skills Error Handling](https://fastmcp.me/Skills/Details/51/error-handling-patterns)
- [Claude Code Skills: Arguments and Placeholders](https://code.claude.com/docs/en/skills)
- [Transcript Processing Best Practices 2025](https://www.airops.com/prompts/transcript-summary-ai-seo-claude-prompts)

---

### Pitfall 5: Tone Consistency Without Templates

**What goes wrong:** Each email has different style, formality, and structure. One email says "Hi John," next says "Dear Mr. Smith," next says "Hey!" Manager sends formal email, then casual follow-up—customer confused about relationship.

**Why it happens:**
- No explicit tone specification in skill
- Claude adapts to perceived context (formal company name → formal tone)
- No examples showing desired style
- Assuming "professional" is self-evident

**Consequences:**
- Brand inconsistency across team
- Customer confusion about relationship formality
- Time wasted on manual editing for tone
- Difficult to maintain "voice" guidelines

**Prevention:**

1. **Explicit tone specification:**
   ```markdown
   ## Postman voice guidelines

   **Tone:** Professional but approachable
   - Use first names: "Hi Sarah," not "Dear Ms. Johnson,"
   - Contractions are fine: "we're" not "we are"
   - Active voice: "I'll send" not "it will be sent"
   - No corporate jargon: "help" not "facilitate"

   **Structure:**
   - Subject line: Action-focused, <60 chars
   - Greeting: First name only
   - Opening: One sentence context
   - Body: 2-3 short paragraphs, <150 words total
   - Close: Clear next step
   - Signature: First name only, no title

   **Never use:**
   - "Circling back"
   - "Reaching out"
   - "Touching base"
   - "Per our conversation"
   ```

2. **Template system with variations:**
   ```markdown
   ## Email templates

   ### Template A: Technical follow-up
   ```
   Subject: [Specific feature] - next steps

   Hi [Name],

   Thanks for the detailed questions about [feature] today.

   [Answer to main question - 1-2 sentences]

   [Additional context if needed - 1-2 sentences]

   [Clear next step]

   [Your first name]
   ```

   ### Template B: Meeting follow-up
   [Similar structure]
   ```

3. **Examples for every template:**
   ```markdown
   ## Example: Technical follow-up

   **Transcript:** Customer asked about webhook retry logic...

   **Generated email:**
   ```
   Subject: Webhook retries - exponential backoff details

   Hi Marcus,

   Thanks for asking about our webhook retry logic today.

   We use exponential backoff: 1s, 2s, 4s, 8s, 16s. After 5
   attempts, webhooks enter a dead-letter queue where you can
   manually retry or inspect failures.

   I'll send over the webhook documentation link separately.
   Let me know if you want to discuss monitoring strategies.

   David
   ```
   ```

4. **Style checking:**
   ```markdown
   ## Style verification

   Before finalizing, check:
   - [ ] Subject line <60 characters
   - [ ] Greeting uses first name only
   - [ ] Total word count <200
   - [ ] No jargon phrases (see banned list)
   - [ ] One clear next step
   - [ ] Signature is first name only
   ```

**Detection warning signs:**
- Emails vary wildly in formality level
- Some emails 50 words, others 300 words
- Inconsistent greetings ("Hi" vs "Dear" vs "Hey")
- Team members say "I had to rewrite it"

**Phase impact:** Foundation for **Phase 3: Template System**. Without this, templates are just examples.

**Sources:**
- [Claude Prompt Engineering: Consistency](https://promptbuilder.cc/blog/claude-prompt-engineering-best-practices-2026)
- [Professional Email Prompts for Claude](https://www.godofprompt.ai/blog/top-12-claude-ai-prompts-for-professional-writing)
- [Transcript Processing Consistency 2025](https://www.promptsty.com/transcript-summarization-ai-prompts)

---

## Common Mistakes (Non-Critical)

### Mistake 1: Skill Triggers Too Often

**What happens:** Skill activates on unrelated requests. User asks general question, skill generates email unexpectedly.

**Prevention:**
- Make description narrow and specific
- Use `disable-model-invocation: true` for manual-only workflows
- Add conditional: "Only when user explicitly mentions [trigger]"

**Phase impact:** Can be tuned in Phase 4 (Testing & Refinement).

---

### Mistake 2: Over-Engineering with Multiple Options

**What happens:** Skill offers 5 different approaches, Claude becomes paralyzed.

**Prevention:**
```markdown
## Bad: Too many choices
"You can use pypdf, or pdfplumber, or PyMuPDF..."

## Good: Default with escape hatch
"Use pdfplumber for text extraction. For scanned PDFs, use pdf2image with pytesseract."
```

**Phase impact:** Phase 2 (Core Generation). Avoid decision paralysis.

---

### Mistake 3: Windows Path Incompatibility

**What happens:** Skill uses `scripts\helper.py` which fails on Mac/Linux.

**Prevention:**
- Always use forward slashes: `scripts/helper.py`
- Test on all platforms before sharing
- Document platform compatibility

**Phase impact:** Phase 1 (Infrastructure). Easy fix, painful if deferred.

---

### Mistake 4: Deep Reference Nesting

**What happens:** SKILL.md → ADVANCED.md → DETAILS.md. Claude partially reads files with `head -100`, missing critical info.

**Prevention:**
- Keep references **one level deep** from SKILL.md
- All reference files link directly from main skill
- Structure with table of contents for >100 line files

**Phase impact:** Phase 3 (Template System) when adding reference files.

---

### Mistake 5: Assuming Package Availability

**What happens:** Skill assumes `pdfplumber` installed, crashes on fresh environment.

**Prevention:**
```markdown
## Dependencies

**Required packages:**
```bash
pip install openai anthropic
```

**Note:** Claude.ai can install from PyPI/npm. API has no network access.
```

**Phase impact:** Phase 1 (Infrastructure). Document early.

---

## Testing Checklist

Before releasing skill, verify:

### Cross-Platform Testing
- [ ] Skill activates in Claude Code (~/.claude/skills/)
- [ ] Skill activates in Claude Desktop (ZIP upload)
- [ ] Skill description matches platform limits (<1024 chars)
- [ ] File paths use forward slashes
- [ ] No platform-specific dependencies without fallbacks

### Activation Testing
- [ ] Skill appears in "What skills are available?"
- [ ] Auto-triggers on 3+ natural phrase variations
- [ ] Doesn't auto-trigger on unrelated requests
- [ ] Manual invocation with `/skill-name` works
- [ ] Description includes keywords users actually say

### Token Budget Testing
- [ ] SKILL.md <500 lines
- [ ] No explanations of concepts Claude already knows
- [ ] Reference files for detailed content
- [ ] Progressive disclosure for optional content

### Incomplete Data Testing
- [ ] Handles transcripts with missing names
- [ ] Handles transcripts with missing next steps
- [ ] Handles transcripts with poor audio quality notes
- [ ] Generates placeholders, doesn't hallucinate
- [ ] Provides placeholder summary after generation
- [ ] Confidence level shown for each output

### Tone Consistency Testing
- [ ] 5 different transcripts → similar email style
- [ ] Subject lines <60 characters
- [ ] Greetings consistent (first name only)
- [ ] Word count consistent (<200 words)
- [ ] No banned jargon phrases
- [ ] Team members don't need to rewrite tone

### Error Handling Testing
- [ ] Empty transcript → graceful error
- [ ] Malformed transcript → placeholder-filled output
- [ ] Network-dependent features → fallback behavior
- [ ] Package import errors → clear error message

---

## Phase-Specific Warnings

| Phase | Likely Pitfall | Mitigation Strategy |
|-------|---------------|---------------------|
| Phase 1: Infrastructure | Cross-platform incompatibility | Design dual-artifact build from start |
| Phase 2: Core Generation | Silent activation failures | Test with 10+ natural trigger phrases |
| Phase 2: Core Generation | Missing data crashes | Build placeholder system before templates |
| Phase 3: Template System | Token budget explosion | Use progressive disclosure, keep main <500 lines |
| Phase 3: Template System | Tone inconsistency | Define voice guidelines with examples |
| Phase 4: Testing | "Works on my machine" | Test on both platforms with fresh installs |
| Phase 5: Deployment | Team installation confusion | Separate docs for Code vs Desktop install |

---

## Open Questions & Research Flags

**LOW confidence areas needing validation:**

1. **Network access in Claude Desktop ZIP:**
   - Can ZIP-deployed skills access external APIs?
   - Or limited to claude.ai-only package installation?
   - **Action:** Test with network call in Phase 1

2. **Character budget impact:**
   - Does increasing `SLASH_COMMAND_TOOL_CHAR_BUDGET` affect performance?
   - At what point do we hit context limits?
   - **Action:** Monitor token usage in Phase 4

3. **Subagent crashes with Skill tool:**
   - Recent bug report (Jan 2026) about subagent crashes
   - Does this affect our use case?
   - **Action:** Avoid subagent patterns until verified stable

---

## Confidence Assessment

| Area | Confidence | Reason |
|------|-----------|--------|
| Cross-platform compatibility | HIGH | Verified with official docs + community reports |
| Skill activation mechanics | HIGH | Official best practices docs + troubleshooting guides |
| Token budget guidance | HIGH | Explicit official guidance (500 line limit) |
| Placeholder handling | MEDIUM | General AI best practices, not skill-specific |
| Tone consistency | MEDIUM | General prompt engineering, verified with 2025-26 sources |
| Network access limits | LOW | Conflicting information, needs testing |
| Subagent stability | LOW | Recent bug reports, ongoing development |

---

## Sources

### Official Documentation (HIGH confidence)
- [Skill Authoring Best Practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Claude Code Skills Documentation](https://code.claude.com/docs/en/skills)
- [Claude Code Troubleshooting](https://code.claude.com/docs/en/troubleshooting)
- [Using Skills in Claude Help Center](https://support.claude.com/en/articles/12512180-using-skills-in-claude)

### Community Resources (MEDIUM-HIGH confidence)
- [Claude Skills Deep Dive](https://leehanchung.github.io/blogs/2025/10/26/claude-skills-deep-dive/)
- [The Definitive Guide to Claude SKILLS](https://limitededitionjonathan.substack.com/p/the-definitive-guide-to-claude-skills)
- [100+ People Hit the Same Claude Skills Problems](https://natesnewsletter.substack.com/p/i-watched-100-people-hit-the-same)
- [Claude Code Email Productivity](https://harper.blog/2025/12/03/claude-code-email-productivity-mcp-agents/)

### Prompt Engineering Resources (MEDIUM confidence)
- [Claude Prompt Engineering Best Practices 2026](https://promptbuilder.cc/blog/claude-prompt-engineering-best-practices-2026)
- [Claude AI Prompts for Professional Writing](https://www.godofprompt.ai/blog/top-12-claude-ai-prompts-for-professional-writing)
- [Transcript Summarization AI Prompts](https://www.promptsty.com/transcript-summarization-ai-prompts)

### Technical Resources (MEDIUM-LOW confidence)
- [Error Handling Patterns - FastMCP](https://fastmcp.me/Skills/Details/51/error-handling-patterns)
- [GitHub Issue #18057: Subagent crashes](https://github.com/anthropics/claude-code/issues/18057)
