# Features Research: Email Drafting Skill

**Domain:** AI-powered email drafting from call transcripts
**Use Case:** Publisher Success managers generating follow-up emails from Gong calls
**Researched:** 2026-01-21
**Overall Confidence:** MEDIUM

## Research Summary

Based on analysis of current AI email drafting tools, Gong's native capabilities, and examination of 14 real follow-up emails from the target user, this research identifies features across three categories: table stakes (must-have), differentiators (nice-to-have), and anti-features (deliberately avoid).

**Key Finding:** In 2026, basic text generation is commodity. Value comes from context extraction accuracy, maintaining human voice, and workflow integration. The skill must generate drafts that require minimal editing while preventing common AI email pitfalls.

---

## Table Stakes

Features users expect. Missing these makes the skill unusable or untrustworthy.

| Feature | Why Expected | Complexity | Notes |
|---------|--------------|------------|-------|
| **Extract action items from transcript** | Core value proposition - transforms call discussion into next steps | Medium | Must distinguish between mentioned tasks and actual commitments. Gong natively does this. |
| **Extract resources/links discussed** | Publisher Success calls routinely reference URLs for docs, examples, workspaces | Medium | Requires identifying URL patterns in transcript. Links often spoken as "postman dot com slash..." |
| **Hyperlink URLs automatically** | URLs in email body must be clickable | Low | Standard markdown/HTML link formatting. PayPal example: `[PayPal](https://developer.paypal.com/api/rest/)` |
| **Generate greeting + thanks** | Every follow-up email starts with personalized greeting and thanks | Low | Pattern: "Hi {Name}, thanks for your time {today/yesterday}! {Nice to meet you / great chatting}." |
| **Configurable signature** | User signature should be consistent and editable | Low | Simple string template. Example: "Best,\nDavid" |
| **Maintain human voice/tone** | AI-generated emails that sound robotic damage trust and deliverability | High | Must match user's existing writing style. Critical for avoiding "uncanny valley" effect. |
| **Save as draft, never send** | Safety requirement - user must review before sending | Low | Fundamental safety principle from Claude Skills best practices. |
| **Extract participant names** | Need to address recipients correctly in greeting | Low | Usually in transcript metadata or early in recording. |
| **Generate subject line** | Professional subject line following pattern "Follow up - {topic/company}" | Low | Pattern from examples: "Following up", "Follow up from {date}", "{Company} - Following up {date}" |
| **Format as structured sections** | Email must have clear sections (greeting, resources, next steps, closing) | Low | Standard template structure already established in project context. |

**Dependencies:**
- Hyperlink URLs depends on Extract resources/links
- Generate greeting depends on Extract participant names
- Maintain human voice affects all text generation features

---

## Differentiators

Features that elevate the skill from functional to excellent. Not required for MVP but significantly increase value.

| Feature | Value Proposition | Complexity | Notes |
|---------|-------------------|------------|-------|
| **Smart attachment reminder** | Detects when deck/PDF was shared in call and reminds user to attach | Medium | Pattern recognition: "Following up with the deck" or "Attaching the PDF" triggers reminder. High ROI - prevents common mistake. |
| **Context-aware resource descriptions** | Add brief context to each link (e.g., "PayPal example https://...") instead of bare URLs | Medium | Significantly improves email scannability. Pattern seen in all real examples. |
| **Distinguish discussed vs committed next steps** | Separate "we talked about X" from "you agreed to Y" | High | Prevents over-promising. Critical for Publisher Success context where commitments matter. |
| **Detect call type and adjust tone** | First meeting = "Nice meeting you", follow-up = "Great chatting as always" | Medium | Pattern visible in examples. Personalization without over-personalization. |
| **Extract and surface meeting cadence** | Identify "let's reconnect in 2 weeks" and include in closing | Medium | Common pattern: "I'll reach out in a couple weeks if we haven't reconnected by then." |
| **CC participant recognition** | Identify which participants should be in To: vs CC: based on conversation role | High | Complex inference. May require explicit user input for MVP. |
| **Suggest follow-up timeline** | Based on discussion, recommend when to send next check-in | Medium | Helps user maintain momentum. Pattern: "Let's stay in touch and reconnect in {timeframe}." |
| **Multi-link formatting optimization** | When 3+ links mentioned, automatically create bullet list instead of paragraph | Low | Improves readability. Clear pattern in examples. |
| **Extract demo/product discussed** | Identify which Postman features were discussed for context | Medium | Helps with personalization and ensures relevant resources are included. |
| **Conditional closing based on next steps** | If calendar invite mentioned: "I'll send over a calendar invite shortly" | Medium | Small touch that shows attention to detail. Pattern in examples. |

**Dependencies:**
- Context-aware resource descriptions depends on Extract resources/links
- Distinguish discussed vs committed depends on Extract action items
- Multi-link formatting depends on Extract resources/links

---

## Anti-Features

Features to deliberately NOT build. Common mistakes in this domain or scope creep risks.

| Anti-Feature | Why Avoid | What to Do Instead |
|--------------|-----------|-------------------|
| **Auto-send without review** | Damages user trust, violates Claude Skills best practices, risks errors in critical business communication | Always save as draft. User review is mandatory. |
| **Over-personalization** | Using recipient name repeatedly feels robotic and damages authenticity. 2026 best practice: personalization should feel natural, not algorithmic. | Use name once in greeting. Avoid "Hey [Name], [Name] I wanted to..." pattern. |
| **Generic template language** | Phrases like "Hope this email finds you well", "Just checking in", "Quick question" are filtered by 2026 AI spam detection | Use specific, contextual openings based on actual call content. |
| **CRM integration (v1)** | Scope creep. Adds complexity without proportional value for greenfield skill. Gong already handles CRM updates. | Focus on email quality. Consider for v2 if users request. |
| **Email scheduling/send timing** | Optimization fatigue. Research shows afternoon > morning but marginal gains vs implementation cost. | User can schedule via email client. Not core value. |
| **Sentiment analysis** | Overkill for Publisher Success context. Calls are professional, not sales objection handling. | Trust transcript content at face value. |
| **Multi-language support** | All reviewed emails are English. No evidence of need. Adds significant complexity. | English-only for MVP. Revisit if international expansion happens. |
| **Email thread detection** | Attempting to reply in existing thread vs new email is complex and error-prone. | Generate fresh email. User can manually thread if needed. |
| **Attachment upload** | Skill can't access local files. Reminder is sufficient. | Smart attachment reminder only. User attaches manually. |
| **HTML email design/templates** | Plain text with hyperlinks is professional standard for B2B follow-ups. HTML templates add complexity and deliverability risk. | Stick to plain text with markdown-style formatting. |
| **Tone adjustment slider** | "Make it more formal/casual" creates decision paralysis and risks over-correction away from user's authentic voice. | Learn user's voice from examples, apply consistently. |
| **Emoji injection** | Not present in any of 14 example emails. Not expected in B2B Publisher Success context. | Professional text only. |
| **Meeting recording analysis** | Gong already provides transcript. Analyzing audio adds no value and significant complexity. | Work from transcript only. |

**Rationale Summary:**

The anti-features list prevents three failure modes:
1. **Safety violations**: Auto-send, no review
2. **Trust damage**: Over-personalization, generic templates, unnatural tone
3. **Scope creep**: CRM integration, scheduling, multi-language, HTML design

---

## Feature Dependencies

### Dependency Graph

```
Core Extraction Layer (must complete first):
├─ Extract participant names
├─ Extract action items from transcript
├─ Extract resources/links discussed
└─ Detect call type

Formatting Layer (depends on extraction):
├─ Hyperlink URLs automatically (depends: Extract resources/links)
├─ Context-aware resource descriptions (depends: Extract resources/links)
├─ Multi-link formatting optimization (depends: Extract resources/links)
├─ Distinguish discussed vs committed (depends: Extract action items)
└─ Generate greeting + thanks (depends: Extract participant names, Detect call type)

Composition Layer (depends on formatting):
├─ Format as structured sections (depends: all formatting features)
├─ Generate subject line (depends: Extract participant names, context)
├─ Smart attachment reminder (depends: transcript analysis)
├─ Conditional closing (depends: Extract action items, meeting cadence)
└─ Maintain human voice/tone (depends: all text generation features)

User Control Layer (independent):
├─ Configurable signature
└─ Save as draft, never send
```

### Critical Path for MVP

**Phase 1: Core Extraction (must-have)**
1. Extract participant names
2. Extract action items from transcript
3. Extract resources/links discussed

**Phase 2: Basic Formatting (must-have)**
4. Hyperlink URLs automatically
5. Generate greeting + thanks
6. Format as structured sections
7. Generate subject line

**Phase 3: Safety & Polish (must-have)**
8. Configurable signature
9. Maintain human voice/tone
10. Save as draft, never send

**Phase 4: Differentiators (nice-to-have)**
11. Smart attachment reminder
12. Context-aware resource descriptions
13. Detect call type and adjust tone
14. Multi-link formatting optimization

---

## MVP Recommendation

For MVP (Minimum Viable Product), prioritize:

### Must Include (Table Stakes):
1. Extract action items from transcript
2. Extract resources/links discussed
3. Hyperlink URLs automatically
4. Generate greeting + thanks
5. Configurable signature
6. Maintain human voice/tone
7. Save as draft, never send
8. Extract participant names
9. Generate subject line
10. Format as structured sections

### Should Include (High-ROI Differentiators):
11. Smart attachment reminder (prevents common error)
12. Context-aware resource descriptions (dramatically improves readability)
13. Multi-link formatting optimization (clear pattern in examples)

### Defer to Post-MVP:
- Detect call type and adjust tone: Nice refinement but not critical
- Distinguish discussed vs committed next steps: Complex, requires deeper NLP
- CC participant recognition: Requires user input or complex inference
- Extract meeting cadence: Pattern-based, good for v2
- Conditional closing: Small polish item
- Suggest follow-up timeline: Helpful but not essential

### Explicitly Exclude:
- All anti-features listed above
- Any feature requiring file system access (attachment upload)
- Any feature requiring external API integration (CRM, calendar, email sending)

---

## Implementation Complexity Notes

### Low Complexity (1-2 days):
- Hyperlink URLs automatically: Standard regex + markdown formatting
- Configurable signature: String template substitution
- Save as draft: Write to file instead of email API
- Generate subject line: Template with variable substitution
- Format as structured sections: Predefined template structure
- Multi-link formatting: List detection + bullet formatting

### Medium Complexity (3-5 days):
- Extract resources/links discussed: Regex patterns + context windows
- Extract action items: Keyword detection + sentence parsing
- Smart attachment reminder: Pattern matching for "attached", "deck", "PDF"
- Context-aware resource descriptions: Link proximity text extraction
- Extract participant names: Transcript header parsing
- Detect call type: Historical context lookup (first meeting vs follow-up)
- Extract meeting cadence: Temporal phrase extraction

### High Complexity (1+ weeks):
- Maintain human voice/tone: Few-shot learning from example emails, style transfer
- Distinguish discussed vs committed: Intent classification, commitment detection
- CC participant recognition: Role inference from conversation dynamics

---

## Validation Criteria

How to know if features are working:

### Table Stakes Validation:
- [ ] Generated email includes all participants in greeting
- [ ] All URLs from transcript appear as clickable hyperlinks
- [ ] Action items section accurately reflects commitments from call
- [ ] Email tone matches user's existing sent emails (Flesch-Kincaid similarity)
- [ ] Draft saved to file, never auto-sent
- [ ] Subject line follows established pattern
- [ ] Signature matches user configuration

### Differentiators Validation:
- [ ] When "deck" or "attached" mentioned, reminder appears in draft
- [ ] Links include brief context (not bare URLs)
- [ ] 3+ links formatted as bullet list automatically
- [ ] First meeting emails say "Nice meeting you" vs "Great chatting" for follow-ups

### Anti-Features Validation (negative tests):
- [ ] Email is never sent automatically
- [ ] Recipient name appears max 1-2 times (greeting + maybe closing)
- [ ] No generic phrases like "Hope this finds you well"
- [ ] No emojis present
- [ ] No attempt to integrate with CRM or calendar
- [ ] Plain text format only (no HTML templates)

---

## Research Confidence Assessment

| Category | Confidence | Rationale |
|----------|------------|-----------|
| Table stakes features | HIGH | Based on analysis of 14 real user emails + Gong official documentation + Claude Skills best practices |
| Differentiators | MEDIUM | Based on pattern recognition in examples + WebSearch for 2026 best practices, but not all verified with authoritative sources |
| Anti-features | HIGH | Based on Claude Skills official docs + 2026 email automation research + analysis of what's NOT in user's real emails |
| Complexity estimates | MEDIUM | Based on general software engineering experience, not specific to this codebase |
| Feature dependencies | HIGH | Logical dependencies clear from feature descriptions and technical requirements |

---

## Sources

### Authoritative Sources (HIGH confidence):
- [Skill authoring best practices - Claude Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Generate a follow-up email - Gong Help](https://help.gong.io/docs/generate-a-follow-up-email)
- [Scientific Tips for Writing the Perfect Follow-Up Sales Email - Gong](https://www.gong.io/blog/7-tips-for-writing-the-perfect-follow-up-sales-email-according-to-science)
- Project file: `follow_up_emails_agentic_context.md` (14 real user emails analyzed)

### Industry Research (MEDIUM confidence):
- [15 Best AI Assistants for Email Productivity in 2026 - Gmelius](https://gmelius.com/blog/best-ai-assistants-for-email)
- [Best AI email assistant in 2026 - Superhuman](https://blog.superhuman.com/best-ai-email-assistant/)
- [Claude Skills and CLAUDE.md: a practical 2026 guide for teams - Gend](https://www.gend.co/blog/claude-skills-claude-md-guide)
- [Getting Claude Code to do my emails - Harper Reed's Blog](https://harper.blog/2025/12/03/claude-code-email-productivity-mcp-agents/)
- [Smart follow-up emails: From Gong calls to AI followup drafts - Zapier](https://zapier.com/templates/details/automate-sales-follow-up-emails-gong-hubspot)

### Best Practices Research (MEDIUM confidence):
- [7 Bad Email Examples and How to Avoid Them (2026) - Sparkle](https://sparkle.io/blog/bad-email-examples/)
- [How to Use AI in Email: 7-Step Guide and Mistakes to Avoid - Gmelius](https://gmelius.com/blog/how-to-use-ai-in-email)
- [You Can't Spell "Email" without "AI": 2026's AI-Driven Email Marketing - Validity](https://www.validity.com/blog/you-cant-spell-email-without-ai-how-to-stay-ahead-in-2026s-ai-driven-email-marketing-landscape/)
- [The 10 Best AI Transcribers for Teams in 2026 - Lindy](https://www.lindy.ai/blog/ai-transcriber-teams)

### Technical References (MEDIUM confidence):
- [Email Format Guide: Best Practices, Tips & Examples (2026) - Elementor](https://elementor.com/blog/email-format-guide/)
- [Hyperlink Best Practices - Towson University](https://www.towson.edu/web-guidelines-resources/content-standards-best-practices/writing/hyperlinks.html)
- [AI Action Item Generator - ScreenApp](https://screenapp.io/features/ai-action-item-generator)

---

## Next Steps for Roadmap Creation

Based on this research, roadmap should structure phases as:

1. **Phase 1: Core Extraction Engine** - Build transcript parsing and entity extraction
   - Addresses: Extract participants, action items, resources/links
   - Risk: Accuracy of extraction from natural language
   - Research flag: May need phase-specific research on Gong transcript format/API

2. **Phase 2: Email Composition** - Template structure and text generation
   - Addresses: Formatting, greeting, structure, subject line
   - Risk: Maintaining human voice/tone (high complexity)
   - Research flag: Voice matching likely needs deeper research

3. **Phase 3: Link Intelligence** - URL handling and context
   - Addresses: Hyperlink formatting, context descriptions, multi-link optimization
   - Risk: Low - straightforward text processing

4. **Phase 4: Smart Polish** - Differentiating features
   - Addresses: Attachment reminders, call type detection, conditional closing
   - Risk: Medium - pattern matching complexity

**Critical for Phase 1:** Understanding Gong transcript format/API will require specific technical research that should happen during roadmap creation or early implementation.

**Critical for Phase 2:** Voice matching approach (few-shot learning vs style transfer vs template-based) should be decided during roadmap creation based on Claude API capabilities research.
