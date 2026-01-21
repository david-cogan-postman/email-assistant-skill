# Phase 1: Single-File MVP - Research

**Researched:** 2026-01-21
**Domain:** Claude Skills for Natural Language Processing and Email Generation
**Confidence:** HIGH

## Summary

Phase 1 implements a single SKILL.md file for Claude Desktop that transforms Gong sales call transcripts into professional follow-up emails. The research confirms this is a straightforward prompt engineering task leveraging Claude's native capabilities—no external libraries, APIs, or complex infrastructure required.

**Key Findings:**
- Claude 4.x models excel at structured information extraction from conversational text without requiring special NLP libraries or fine-tuning
- SKILL.md format provides a well-documented, single-file deployment mechanism for Claude Desktop
- Email generation is a pure prompt engineering task—Claude natively handles markdown, maintains context, and follows complex formatting rules
- The real challenge is prompt design, not technical implementation

**Primary recommendation:** Implement as a focused SKILL.md file (under 500 lines) using few-shot prompting with examples from `follow_up_emails_agentic_context.md`. Leverage Claude 4.5's precise instruction following and structured output capabilities.

## Standard Stack

### Core Technology
| Component | Version/Type | Purpose | Why Standard |
|-----------|--------------|---------|--------------|
| SKILL.md file | Agent Skills format | Claude Desktop skill definition | Official Anthropic format for extending Claude |
| Claude 4.5 Sonnet | claude-sonnet-4-5-20250929 | LLM for processing and generation | Balanced performance, precise instruction following |
| Markdown | Standard markdown syntax | Hyperlink formatting | Universal format, works across email clients |

### No External Dependencies Required

This phase requires **zero external libraries, APIs, or tools**. The entire implementation is:
- Pure prompt engineering in SKILL.md
- Claude's native text processing capabilities
- Standard markdown syntax for hyperlinks

**Why this works:**
- Claude 4.x models have native strong capabilities for information extraction, structured output generation, and multi-turn reasoning
- Gong transcripts are well-formatted conversational text (speaker-based dialogues with timestamps)
- Email generation from structured data is a core LLM capability
- Markdown hyperlink syntax is universal: `[text](url)`

### Alternative Approaches (Not Recommended)

| Approach | Why Avoid |
|----------|-----------|
| External NLP libraries (spaCy, NLTK) | Over-engineering; Claude handles extraction natively |
| Structured prompting libraries | Adds complexity; Claude 4.x has native structured output |
| Template engines (Jinja2, Handlebars) | Unnecessary; Claude generates natural prose better than templates |
| Fine-tuned models | Prompt engineering sufficient; no training data needed |

## Architecture Patterns

### Recommended SKILL.md Structure

```
emailSkill/
└── SKILL.md                 # Main skill file (~300-400 lines)
    ├── [Frontmatter]        # YAML: name, description
    ├── [Core Instructions]  # What to extract, how to format
    ├── [Few-Shot Examples]  # 2-3 complete examples from real emails
    ├── [Formatting Rules]   # Hyperlinks, placeholders, signatures
    └── [Edge Cases]         # Missing data, multiple recipients
```

### Pattern 1: Few-Shot Prompting with Real Examples

**What:** Include 2-3 complete input/output examples directly in SKILL.md
**When to use:** Always—Claude 4.x models learn format/tone from examples better than from descriptions
**Source:** Claude prompt engineering best practices

**Example structure:**
```markdown
## Example 1: Standard Follow-Up

**Input transcript:**
[Paste representative Gong transcript excerpt]

**Expected output:**
[Paste corresponding email from follow_up_emails_agentic_context.md]

**Key aspects:**
- Extracted participants: Vincent, Aymen
- Resources formatted as hyperlinked text
- Action items with ownership attribution
```

**Why this works:**
- Claude 4.x models are "vigilant with examples & details" (official docs)
- Few-shot prompting creates internal structure through repeated patterns
- Real examples capture nuance better than abstract rules

### Pattern 2: Structured Extraction Schema

**What:** Define explicit JSON-like schema for extracted information
**When to use:** Before email generation—extract structured data first, then generate prose
**Source:** Structured prompting techniques for LLMs

**Two-phase approach:**
```markdown
## Phase 1: Extract Information

From the transcript, identify:
- **Participants**: Names and roles/companies (internal vs external)
- **Resources**: URLs mentioned, topics discussed
- **Action Items**: Tasks, owners, timelines
- **Call Context**: Date/timing, purpose, key decisions

## Phase 2: Generate Email

Use extracted information to create email following format...
```

**Why this works:**
- Separation of concerns: extraction vs generation
- Easier to debug and refine
- Claude 4.5 excels at structured information extraction
- Intermediate structure prevents hallucination in generation phase

### Pattern 3: Progressive Refinement

**What:** Ask user for missing critical information before generating
**When to use:** When recipient name is unclear or multiple potential recipients exist
**Source:** Context decisions—"Ask user to provide name before generating"

**Implementation:**
```markdown
## Before Generating

If recipient name is unclear:
1. List potential recipients found in transcript
2. Ask user: "Who should I address this email to?"
3. Only proceed after user confirms

NEVER use [NAME] placeholder in greeting—this must be real.
```

**Why this works:**
- Prevents awkward placeholder greetings
- User interaction is cheap compared to regenerating entire email
- Aligns with user expectation from context decisions

### Anti-Patterns to Avoid

- **Over-specification of format:** Claude 4.5 models can over-engineer if prompted too rigidly. Provide examples and principles, not rigid templates.
- **Template-style generation:** Don't use `[FIELD_1]` `[FIELD_2]` approaches. Claude generates more natural prose from principles + examples.
- **Assuming tools are needed:** Don't reach for external libraries. This is pure prompt engineering.
- **Markdown in prompts:** Remove markdown from skill instructions to reduce markdown in outputs (per Claude 4.x best practices)

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Information extraction from transcripts | Custom regex/parsing logic | Claude's native extraction with structured prompts | Claude handles conversational context, speaker attribution, implicit references better than pattern matching |
| Email template engine | String interpolation system | Claude's generative capabilities with few-shot examples | Natural language generation produces more professional, context-aware prose than templates |
| Markdown link validation | URL parsing/checking code | Trust Claude + user review | Over-validation adds complexity; user will review before sending anyway |
| Multi-turn conversation management | Custom state machine | Claude's native context window | Claude 4.5 maintains state across turns automatically |

**Key insight:** This entire phase is about leveraging what Claude already does exceptionally well (information extraction, natural language generation, instruction following). The skill author's job is precise prompting, not building infrastructure.

## Common Pitfalls

### Pitfall 1: Over-Engineering the Solution
**What goes wrong:** Developers reach for external tools (NLP libraries, template engines, validation frameworks) because the task "feels complex"
**Why it happens:** Instinct from traditional software development where you build infrastructure
**How to avoid:** Start with pure prompt engineering. Only add tools if prompting demonstrably fails after iteration.
**Warning signs:** Considering pip installs, thinking about Python scripts, designing data structures

**Prevention strategy:**
- Write the simplest possible SKILL.md first (~200 lines: instructions + 2 examples)
- Test with real Gong transcripts
- Only add complexity if specific failure patterns emerge

### Pitfall 2: Vague or Verbose Instructions
**What goes wrong:** SKILL.md becomes 1000+ lines of detailed explanations, or uses vague guidance like "extract relevant information"
**Why it happens:** Uncertainty about what Claude needs to know; trying to document every edge case
**How to avoid:** Follow the "concise is key" principle—Claude is already smart. Include 2-3 concrete examples instead of extensive explanation.
**Warning signs:** Instructions over 500 lines, no examples, abstract language

**Example of the problem:**
```markdown
<!-- BAD: Too verbose -->
Emails are professional communications that serve to follow up after
sales calls. They typically include multiple sections such as a greeting,
body paragraphs, and a closing. You should extract information from the
transcript including but not limited to participant names, which can be
found by looking for speaker identifiers...
```

**Better approach:**
```markdown
<!-- GOOD: Concise with example -->
Extract from transcript and generate follow-up email.

[Example 1: Complete input transcript → output email]
[Example 2: Complete input transcript → output email]

Format resources as [text](url), use [PLACEHOLDER] for missing info.
```

### Pitfall 3: Template-Style Output Generation
**What goes wrong:** Skill generates emails that feel robotic, with awkward phrasing like "Resource 1: [description]. Resource 2: [description]."
**Why it happens:** Trying to enforce rigid structure instead of letting Claude use natural language
**How to avoid:** Provide example emails as few-shot learning. Claude will match their natural, professional tone.
**Warning signs:** Output feels "AI-generated," lacks warmth, uses numbering where prose would work better

**From context decisions:**
- "Bullet list for 3+ items; inline prose acceptable for fewer items"
- "Add brief context only when not obvious from link text"
- Tone: "Professional friendly—match examples in follow_up_emails_agentic_context.md"

This indicates the emails should feel hand-written, not template-generated.

### Pitfall 4: Ignoring Claude 4.5-Specific Behaviors
**What goes wrong:** Instructions work inconsistently; Claude is too conservative or too aggressive
**Why it happens:** Claude 4.5 has distinct characteristics (more concise, precise instruction following, sensitive to example details)
**How to avoid:** Explicitly adapt prompting for Claude 4.5 models per official best practices
**Warning signs:** Claude skips steps, doesn't extract enough detail, or over-engineers simple cases

**Key Claude 4.5 adaptations:**
- **Be explicit:** "Include as many relevant details from the transcript as possible" (not just "extract information")
- **Match prompt style to output:** Use professional prose in instructions to encourage professional prose in output
- **Provide context for rules:** "Use [PLACEHOLDER] for missing info so user can easily find and fill gaps" (not just "use placeholders")
- **Use examples over rules:** Show 2-3 complete emails rather than describing format in detail

### Pitfall 5: Hallucinating Links and Information
**What goes wrong:** Claude invents URLs, fabricates action items, or creates content not in transcript
**Why it happens:** Generative models can "fill in" missing information when uncertain
**How to avoid:** Explicit instructions to use `[PLACEHOLDER]` markers and `https://placeholder` for unknown data
**Warning signs:** Generated emails contain information not mentioned in transcript

**Prevention strategy:**
```markdown
CRITICAL: Never hallucinate or invent information.

- For missing URLs: Use https://placeholder as URL
- For unclear timelines: Use [TIMELINE]
- For ambiguous owners: Use [OWNER]
- If info isn't in transcript: Mark it clearly rather than guessing
```

**From official docs:** "Minimizing hallucinations in agentic coding" guidance applies here—never speculate about content you haven't seen.

## Code Examples

### Example 1: SKILL.md Frontmatter
```yaml
---
name: gong-follow-up-email
description: Generate professional follow-up emails from Gong sales call transcripts. Use when user pastes a Gong transcript and needs a ready-to-send follow-up email with extracted participants, action items, and resources.
---
```

**Source:** Official Claude Skills documentation - [Skill authoring best practices](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

**Why this works:**
- `name`: Descriptive gerund form (recommended naming convention)
- `description`: Includes both what it does ("generate emails") and when to use ("when user pastes Gong transcript")
- Third-person voice (required for proper skill discovery)

### Example 2: Core Instruction Structure
```markdown
# Gong Follow-Up Email Generator

You help sales professionals create follow-up emails from Gong call transcripts.

## Your Task

1. Extract key information from the pasted Gong transcript
2. Generate a professional follow-up email matching the tone and format of the examples below
3. Ask the user for recipient name if unclear before generating

## Information to Extract

From the transcript, identify:
- **Participants**: Internal team members and external contacts
- **Resources**: URLs and topics discussed that should be shared
- **Action Items**: Next steps, owners, and timelines
- **Call Context**: When the call happened (for timing language)

## Email Format

[Insert 2-3 complete examples from follow_up_emails_agentic_context.md here]

## Formatting Rules

- **Resources**: Always format as hyperlinked text: [descriptive text](url)
  - Good: "verification checklist" linked to URL
  - Bad: Bare URL or [URL](URL)
- **Missing URLs**: Use https://placeholder so structure remains editable
- **Missing Info**: Use [PLACEHOLDER], [TIMELINE], [OWNER] markers
- **Signature**: Use "Best,\nDavid" unless user specifies otherwise
- **Greeting**:
  - Single recipient: "Hi {FirstName},"
  - Multiple: "Hi {Company} team,"
  - NEVER generate: Ask user if unclear

## Before Generating

If recipient is unclear, ask: "Who should I address this email to?"
List potential recipients you found in the transcript.
```

**Source:** Combination of Claude 4.5 best practices and few-shot prompting patterns

**Why this structure works:**
- Clear sequential steps (Claude 4.5 follows explicit workflows)
- Concrete examples over abstract rules
- Handles edge cases (missing info, unclear recipients)
- Matches official guidance: "Be explicit with your instructions"

### Example 3: Handling Markdown Hyperlinks
```markdown
## Resource Link Format

Transform resources into natural hyperlinked text:

**From transcript:**
"I'll send you the verification checklist at postman.com/publisher/settings/verification"

**In email:**
- verification checklist (https://www.postman.com/publisher/settings/verification)

**How to format:**
Use descriptive text from the conversation, not the URL itself.
Format as: [descriptive text](url)

**If URL is missing:**
Use https://placeholder to maintain hyperlink structure:
- verification checklist (https://placeholder)

This allows user to easily find and update the URL manually.
```

**Source:** Context decisions + markdown email compatibility research

**Why this works:**
- Shows transformation from transcript → email
- Explains *why* placeholder URLs help (user can find/fix them)
- Concrete before/after examples

## State of the Art

| Technique | Current Approach (2026) | Previous Approach | Impact |
|-----------|-------------------------|-------------------|--------|
| Transcript extraction | Claude 4.x with structured prompting | Fine-tuned NER models, regex patterns | Native LLM understanding of context, no training data needed |
| Email generation | Few-shot prompting with real examples | Template engines with slot-filling | More natural, context-aware prose |
| Skill deployment | Single SKILL.md file | Custom tools/plugins with dependencies | Zero infrastructure, works in Claude Desktop immediately |
| Structured output | Native Claude 4.5 capabilities | External validation libraries, JSON Schema | Simpler, more reliable |
| Multi-turn refinement | Claude's context awareness | Custom state management | Built-in, no code required |

**Key Shift:** The move from "building infrastructure" to "prompting effectively" for NLP tasks. Claude 4.x models have internalized capabilities that previously required specialized tools.

**Deprecated/outdated approaches:**
- **Fine-tuning for extraction tasks:** Prompt engineering with Claude 4.5 achieves comparable results without training data
- **Template-based email generation:** LLM generation produces more natural output
- **External markdown processors:** Claude natively handles markdown syntax
- **Regex-based transcript parsing:** Context-aware LLM extraction is more robust

## Open Questions

### Question 1: Optimal Number of Few-Shot Examples
**What we know:** Claude 4.5 learns from examples; official guidance says "include examples when helpful"
**What's unclear:** Whether 2, 3, or 5 examples provides best results for this specific task
**Recommendation:** Start with 2-3 representative examples from `follow_up_emails_agentic_context.md`. Can A/B test during iteration if quality issues emerge. More than 5 risks bloating SKILL.md beyond 500 lines.

### Question 2: Handling Very Long Transcripts
**What we know:** Claude 4.5 has large context window; typical Gong transcripts fit easily
**What's unclear:** Behavior with 2+ hour calls generating 50K+ token transcripts
**Recommendation:** Design for typical 30-60 minute calls (the majority per Gong documentation). If long transcripts become an issue, can add guidance: "Summarize key points if transcript exceeds [length]"

### Question 3: Signature Configuration Storage
**What we know:** Default signature is "Best,\nDavid" per requirements
**What's unclear:** How users will specify alternative signatures (inline during conversation? separate config?)
**Recommendation:** Start with inline approach: "If user says 'use signature: [text]', store and reuse for session." Can add persistent storage in Phase 2 if needed.

### Question 4: Effectiveness of Placeholder Strategy
**What we know:** Context decisions specify `[PLACEHOLDER]` markers and `https://placeholder` for missing data
**What's unclear:** Whether users will find these helpful or annoying in practice
**Recommendation:** Implement as specified, but track user feedback. Alternative could be asking for missing info before generating, but this adds friction. Placeholders allow "generate fast, edit after" workflow.

## Sources

### Primary (HIGH confidence)

**Official Anthropic Documentation:**
- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills) - SKILL.md structure, frontmatter, runtime environment
- [Skill authoring best practices - Claude Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) - Conciseness principles, progressive disclosure, naming conventions, examples pattern
- [Prompting best practices - Claude Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-4-best-practices) - Claude 4.5-specific guidance on explicit instructions, examples vigilance, avoiding hallucinations
- [Anthropic Skills GitHub - skill-creator/SKILL.md](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md) - Official skill structure example

**Project Context:**
- `follow_up_emails_agentic_context.md` - 14 real email examples showing exact tone, format, and structure to emulate
- `.planning/phases/01-single-file-mvp/01-CONTEXT.md` - User decisions on tone, formatting, name handling, resource linking

### Secondary (MEDIUM confidence)

**Verified Web Research:**
- [Claude Skills and CLAUDE.md: practical 2026 guide](https://www.gend.co/blog/claude-skills-claude-md-guide) - Skill structure and best practices (aligns with official docs)
- [Information Extraction from Conversation Transcripts (arXiv 2025)](https://arxiv.org/html/2510.12023) - LLM vs neuro-symbolic approaches for transcript parsing
- [Meeting summarization with Amazon Nova (AWS Blog 2026)](https://aws.amazon.com/blogs/machine-learning/meeting-summarization-and-action-item-extraction-with-amazon-nova/) - Action item extraction patterns
- [Gong AI Notetaker Review 2026 (Lindy)](https://www.lindy.ai/blog/gong-note-taker) - Gong transcript structure: Smart Brief with Problem/Solution/Next Steps sections
- [Structured Prompting Techniques: XML & JSON (CodeConductor 2026)](https://codeconductor.ai/blog/structured-prompting-techniques-xml-json/) - Few-shot + structured output patterns

**Few-Shot Prompting & Structured Output:**
- [Mastering Prompt Engineering: Using LLMs to Generate JSON-Based Prompts (Republic Labs 2026)](https://blog.republiclabs.ai/2026/01/mastering-prompt-engineering-using-llms.html) - JSON output patterns with LLMs
- [The Structured Prompting Trick (VibePanda 2025)](https://www.vibepanda.io/resources/guide/json-prompting-beginners-guide-2025) - Start with clear template, use few-shot examples

### Tertiary (LOW confidence - informational only)

- [Markdown Here Extension Compatibility](https://github.com/adam-p/markdown-here/wiki/Compatibility) - Gmail/Outlook markdown support via extensions (not native)
- [Placeholder Best Practices (Lipsum Hub 2026)](https://blog.lipsumhub.com/what-to-write-in-placeholders/) - Clear, specific placeholder guidance
- Various web search results on NLP extraction techniques, conversation intelligence tools

## Metadata

**Confidence breakdown:**
- **Standard stack: HIGH** - SKILL.md format is official Anthropic standard, well-documented with examples
- **Architecture patterns: HIGH** - Based directly on official Claude 4.5 best practices, few-shot prompting is proven technique
- **Extraction/generation approach: HIGH** - Claude 4.x capabilities for structured extraction verified in official docs and recent research
- **Markdown hyperlinks: MEDIUM** - Gmail doesn't natively support markdown, but users will paste into rich text editor (hyperlinks work after manual formatting)
- **Edge cases (long transcripts, signature config): MEDIUM** - Designing for common cases, may need refinement based on real usage

**Research date:** 2026-01-21
**Valid until:** ~30 days (Claude prompt engineering practices stable; skill format stable)
**Recommended re-research trigger:** If users report consistent issues with extraction quality, placeholder approach, or email tone
