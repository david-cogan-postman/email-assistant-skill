# Phase 2: Robust Handling - Research

**Researched:** 2026-01-21
**Domain:** Intelligent Transcript Analysis, Graceful Degradation, Contextual Resource Matching
**Confidence:** HIGH

## Summary

Phase 2 enhances the MVP skill with intelligent handling of incomplete transcripts, flexible activation detection, contextual Postman resource suggestions, and attachment reminders. The research confirms this remains a pure prompt engineering challenge—no external libraries required. The key technical domains are:

1. **Intent Recognition**: Detecting flexible invocation patterns without strict keyword matching
2. **Entity Extraction with Missing Data**: Extracting names, action items, and topics even when transcripts are incomplete
3. **Graceful Degradation**: Asking clarifying questions while still generating drafts
4. **Keyword Detection**: Identifying attachment mentions using semantic patterns
5. **Contextual Resource Matching**: Mapping conversation topics to relevant Postman documentation links

**Key Findings:**
- Claude 4.5 excels at inferring user intent from context without requiring exact phrasing
- Structured prompting with XML tags enables conditional logic and multi-stage processing
- LLMs naturally handle missing data when given explicit instructions and fallback patterns
- Curated knowledge bases (Postman resource library) work better than dynamic search for domain-specific suggestions
- Combining keyword detection with semantic understanding creates robust attachment reminders

**Primary recommendation:** Enhance SKILL.md with XML-structured conditional logic, explicit "ask before generate" patterns, and an embedded Postman resource library. Use Claude's native capabilities for intent detection, entity extraction, and contextual matching—no external NLP tools needed.

## Standard Stack

### Core Components (No Change from Phase 1)

| Component | Version/Type | Purpose | Why Standard |
|-----------|--------------|---------|--------------|
| SKILL.md file | Agent Skills format | Enhanced skill definition with conditional logic | Official Anthropic format, supports complex prompts |
| Claude 4.5 Sonnet | claude-sonnet-4-5-20250929 | LLM for processing, extraction, and generation | Native intent recognition, structured output, context awareness |
| XML tags | Standard syntax | Structure complex prompts with conditional sections | Recommended by Anthropic for clarity and accuracy |
| Markdown | Standard syntax | Hyperlink formatting in emails | Universal format |

### Prompt Engineering Patterns

| Pattern | Purpose | When to Use |
|---------|---------|-------------|
| XML-structured prompts | Organize conditional logic, multiple processing stages | When skill has decision points (ask vs generate, resource matching, keyword detection) |
| Explicit clarification protocol | Ask user questions before generating | When recipient unclear, action items ambiguous, transcript seems incomplete |
| Embedded knowledge base | Curated Postman resource library in prompt | For contextual resource suggestions without external search |
| Semantic keyword detection | Combine literal keywords with context understanding | Detect attachment mentions that use varied phrasing |
| Progressive disclosure | Start simple, add detail only when needed | Keep SKILL.md maintainable while handling edge cases |

### No External Dependencies Required

This phase still requires **zero external libraries, APIs, or tools**:
- Pure prompt engineering in enhanced SKILL.md
- Claude's native intent recognition and entity extraction
- Embedded resource library (static data in prompt)
- Standard markdown syntax

**Why this still works:**
- Claude 4.5 has native strong intent inference and flexible pattern matching
- XML tags provide sufficient structure for conditional logic
- Asking clarifying questions is a built-in Claude capability
- Static resource libraries embedded in prompts work well for bounded domains (Postman docs)

### Alternative Approaches (Not Recommended)

| Approach | Why Avoid |
|----------|-----------|
| External intent detection libraries | Over-engineering; Claude handles flexible intent natively |
| RAG/vector database for resources | Adds infrastructure; curated list in prompt sufficient for Postman docs |
| Regex for keyword detection | Brittle; Claude understands semantic variations better |
| External validation APIs | Unnecessary; user reviews output anyway |
| State management libraries | Claude maintains context across turns natively |

## Architecture Patterns

### Recommended Enhanced SKILL.md Structure

```
emailSkill/
└── SKILL.md                           # Enhanced main skill file (~600-800 lines)
    ├── [Frontmatter]                  # YAML: name, description (unchanged)
    ├── <activation_detection>         # Intent recognition patterns
    ├── <extraction_instructions>      # Entity extraction with fallbacks
    ├── <clarification_protocol>       # When/how to ask questions
    ├── <resource_library>             # Embedded Postman documentation links
    ├── <resource_matching>            # Topic → resource mapping logic
    ├── <attachment_detection>         # Keyword patterns and triggers
    ├── <generation_instructions>      # Email generation with placeholders
    ├── [Few-Shot Examples]            # 2-3 complete examples (from Phase 1)
    └── <edge_case_handling>           # Incomplete transcripts, missing data
```

### Pattern 1: XML-Structured Conditional Logic

**What:** Use XML tags to organize different processing stages and decision points
**When to use:** Always for Phase 2—skill now has multiple conditional paths (ask vs generate, resource matching, attachment detection)
**Source:** [Claude Docs - Use XML tags](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/use-xml-tags)

**Example structure:**
```xml
<activation_detection>
## Detecting User Intent

User must clearly indicate they want to use this skill. Look for:
- Explicit mentions: "skill", "follow-up", "draft email from this transcript"
- Context clues: Pasting transcript + requesting email generation
- Ambiguous cases: "Help me with this call" + transcript paste

### Decision Logic

IF invocation is CLEAR:
  → Proceed to extraction

IF invocation is UNCLEAR but seems intended:
  → Ask: "Did you want me to generate a follow-up email?"

IF transcript pasted WITHOUT invocation:
  → Suggest: "Would you like me to draft a follow-up email from this transcript?"
</activation_detection>

<extraction_instructions>
## Extract Information (Even if Incomplete)

From the transcript, identify:

<recipients>
- Primary recipient name(s)
- Company affiliation
- Role if mentioned

IF UNCLEAR: Mark for clarification, but continue extraction
</recipients>

<action_items>
- Tasks mentioned
- Owner attribution (if clear)
- Timeline (if mentioned)

IF AMBIGUOUS: Extract all possible items, will clarify with user
</action_items>

<topics>
- Technical topics discussed (for resource matching)
- Problems mentioned
- Solutions proposed

This section drives resource suggestions - extract generously
</topics>
</extraction_instructions>
```

**Why this works:**
- XML tags prevent Claude from mixing instructions with data
- Clear section boundaries enable modular prompt design
- Nested tags create hierarchical logic (outer: extraction, inner: recipients/action items/topics)
- References to tag names ("Using the topics in `<topics>` tags...") ensure Claude processes sections correctly

**Source verification:** Official Anthropic documentation states XML tags provide "clarity, accuracy, flexibility, and parseability" and are recommended for complex prompts.

### Pattern 2: Ask-Then-Generate Protocol

**What:** Explicitly instruct Claude when to ask clarifying questions vs. when to proceed with generation
**When to use:** Always—Phase 2 requirement is "asking for more is additive, not blocking"
**Source:** User decisions in CONTEXT.md + [Claude Code AskUserQuestion patterns](https://www.atcyrus.com/stories/claude-code-ask-user-question-tool-guide)

**Implementation:**
```xml
<clarification_protocol>
## When to Ask Questions

Questions are ADDITIVE, not BLOCKING. Always generate a draft regardless.

### Recipient Name Unclear
IF recipient cannot be confidently identified:
1. Ask: "Who is the recipient? I found these people in the transcript: [list names]"
2. Wait for user response
3. Regenerate greeting with confirmed name

FALLBACK: Use "[NAME]" placeholder if user doesn't respond, but try asking first

### Action Items Ambiguous
IF action items are unclear or have multiple interpretations:
1. Present options: "I found these possible action items - which should I include?"
2. Wait for user confirmation
3. Update email with confirmed items

FALLBACK: Include all plausible action items in draft with [OWNER] placeholders

### Transcript Seems Incomplete
IF transcript appears cut off or missing context:
1. Note the issue: "This transcript seems incomplete. I'll generate a draft based on what's here. Would you like to provide more context?"
2. Generate draft immediately
3. If user provides more context, regenerate

NEVER block generation waiting for more information.
</clarification_protocol>
```

**Why this works:**
- Explicit IF/THEN logic maps to Claude 4.5's precise instruction following
- "Generate regardless" prevents blocking behavior
- FALLBACK patterns ensure graceful degradation
- Asking questions improves accuracy without adding friction

**User expectation alignment:** Context decisions specify "ask before generating" for names and "always generate a draft no matter what" for incomplete transcripts. This pattern reconciles both requirements.

### Pattern 3: Embedded Knowledge Base for Resource Matching

**What:** Include curated Postman resource library directly in prompt, with topic-to-resource mapping
**When to use:** For bounded domains with stable documentation (Postman resources fit this perfectly)
**Source:** [RAG and curated knowledge bases for LLMs](https://www.scoutos.com/blog/top-5-llm-prompts-for-retrieval-augmented-generation-rag) + [Context Engineering Guide](https://www.promptingguide.ai/guides/context-engineering-guide)

**Implementation:**
```xml
<resource_library>
## Postman Documentation Resources

Clean, curated sources for contextual suggestions.

### Verification & Publishing
- Postman Verification Checklist: https://www.postman.com/publisher/settings/verification
- Publisher Settings: https://www.postman.com/publisher/settings
- Public API Network: https://www.postman.com/explore

### Notebooks & Collections
- Creating Notebooks: https://learning.postman.com/docs/publishing-your-api/creating-notebooks/
- Visualizer Documentation: https://learning.postman.com/docs/sending-requests/visualizer/
- Collection Runs: https://learning.postman.com/docs/running-collections/intro-to-collection-runs/

### Testing & Automation
- Writing Tests: https://learning.postman.com/docs/writing-scripts/test-scripts/
- Newman CLI: https://learning.postman.com/docs/running-collections/using-newman-cli/
- Postman API: https://www.postman.com/postman/postman-public-workspace/api/postman-api

### Collaboration Features
- Workspaces: https://learning.postman.com/docs/collaborating-in-postman/using-workspaces/
- Version Control: https://learning.postman.com/docs/collaborating-in-postman/version-control/
- Comments & Tagging: https://learning.postman.com/docs/collaborating-in-postman/working-with-your-team/commenting/

### Run in Postman
- Run in Postman Button: https://learning.postman.com/docs/publishing-your-api/run-in-postman/
- Embed Collections: https://learning.postman.com/docs/publishing-your-api/run-in-postman/introduction-run-button/

### Enterprise Features
- SSO Configuration: https://learning.postman.com/docs/administration/sso/intro-sso/
- SCIM Provisioning: https://learning.postman.com/docs/administration/scim-provisioning/
- Audit Logs: https://learning.postman.com/docs/administration/managing-your-team/audit-logs/
</resource_library>

<resource_matching>
## Contextual Resource Suggestions

Based on topics extracted from <topics>, suggest relevant resources.

### Matching Logic

IF transcript mentions ["verification", "publishing", "public API", "submit"]:
  → Include Verification Checklist, Public API Network

IF transcript mentions ["notebook", "documentation", "visualize", "markdown"]:
  → Include Creating Notebooks, Visualizer Documentation

IF transcript mentions ["test", "automation", "CI/CD", "script"]:
  → Include Writing Tests, Newman CLI, Collection Runs

IF transcript mentions ["embed", "share", "button", "integrate"]:
  → Include Run in Postman Button, Embed Collections

IF transcript mentions ["enterprise", "SSO", "SCIM", "audit"]:
  → Include SSO Configuration, SCIM Provisioning, Audit Logs

IF transcript mentions ["workspace", "collaborate", "team", "comment"]:
  → Include Workspaces, Comments & Tagging

### Suggestion Format

- Auto-include directly relevant links inline in email body (1-3 links)
- Group additional "also useful" resources in separate section if 4+ links
- No hard limit - typically 3-7 based on call content
- Use descriptive text from conversation as link anchor

EXAMPLE:
"I mentioned the [verification checklist](https://www.postman.com/publisher/settings/verification) during the call..."

OR for multiple resources:

"Here are the resources we discussed:
- [Verification checklist](url) for publishing your API
- [Creating Notebooks](url) for documentation
- [Run in Postman button](url) to embed collections"
</resource_matching>
```

**Why this works:**
- Curated list ensures quality, accuracy (no hallucinated links)
- Topic-based matching is deterministic and explainable
- Embedded in prompt = zero latency, no API calls
- Maintainable: add new resources to `<resource_library>` as Postman documentation evolves
- Bounded domain: Postman docs are stable, comprehensive list fits in prompt

**Trade-offs vs. RAG/vector search:**
- PRO: No infrastructure, instant matching, no hallucination risk
- PRO: Curated = higher quality than search results
- CON: Manual maintenance when new resources added (acceptable for stable documentation)
- CON: Doesn't scale to thousands of resources (not needed for Postman use case)

**Source verification:** Research on [context-aware recommendation systems](https://link.springer.com/article/10.1007/s10462-024-10939-4) and [LLM knowledge bases](https://slite.com/en/learn/llm-knowledge-base) confirms curated knowledge bases outperform dynamic search for bounded domains with stable content.

### Pattern 4: Semantic Keyword Detection for Attachments

**What:** Detect attachment mentions using both literal keywords and contextual understanding
**When to use:** Always—Phase 2 requirement for attachment reminders
**Source:** User decisions in CONTEXT.md + [NOVA framework combining keywords with semantic matching](https://github.com/fr0gger/nova-framework)

**Implementation:**
```xml
<attachment_detection>
## Attachment Reminder Logic

Trigger reminders when transcript mentions BOTH presentation words AND sharing words.

### Trigger Keywords

PRESENTATION_WORDS = ["deck", "slides", "presentation", "PDF", "document", "file"]
SHARING_WORDS = ["send over", "send you", "share", "attached", "attach", "I'll send", "forward"]

### Detection Logic

IF (PRESENTATION_WORD mentioned) AND (SHARING_WORD mentioned):
  → Add attachment placeholder to email draft

IF (PRESENTATION_WORD mentioned) BUT NO (SHARING_WORD):
  → Do NOT add placeholder (just referenced, not promised)

IF multiple attachments mentioned:
  → Add separate placeholder for each item

### Placeholder Format

Use natural context from call to name attachment:

EXAMPLE FROM TRANSCRIPT:
"I'll send over the onboarding deck after the call"

PLACEHOLDER IN EMAIL:
"[ATTACH: onboarding deck]"

Place placeholder where it fits naturally in email flow:
- Inline: "I've attached the [ATTACH: onboarding deck] we discussed."
- Separate line: "[ATTACH: quarterly review slides]"
- End of action items: "• I'll send the [ATTACH: integration guide] by Friday"

### Semantic Understanding

Look for conceptual matches, not just exact phrases:

"I'll get you that slide deck" = SHARING_WORD (implied "send")
"The PDF I mentioned" = PRESENTATION_WORD (specific format)
"Forward the materials" = SHARING_WORD (forwarding = sharing)

Use Claude's natural language understanding - don't require exact keyword matches.
</attachment_detection>
```

**Why this works:**
- Combining keyword categories (presentation + sharing) reduces false positives
- Semantic matching ("I'll get you" = sharing) handles natural language variations
- Context-aware naming ("[ATTACH: onboarding deck]") more useful than generic "[ATTACHMENT]"
- Flexible placement integrates reminders naturally into email flow
- Claude 4.5 excels at this type of contextual pattern matching without regex

**Source verification:** Research on [regex for NLP tasks](https://www.analyticsvidhya.com/blog/2021/06/regex-cheatsheet-for-natural-language-processing-tasks/) shows traditional keyword matching works for structured patterns. The [NOVA framework](https://github.com/fr0gger/nova-framework) demonstrates combining keyword detection with semantic similarity and LLM-based matching for robust pattern recognition.

### Pattern 5: Progressive Disclosure for Edge Cases

**What:** Keep main instructions concise, add detail only in edge case sections
**When to use:** Always—prevents SKILL.md from becoming overwhelming
**Source:** [Claude Skills Best Practices - Progressive Disclosure](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)

**Structure:**
```markdown
## Core Instructions
[Simple, clear main path - 200 lines]

<edge_cases>
## Handling Special Cases

Only reference these when encountered:

### Very Short Transcripts (< 5 exchanges)
IF transcript is very brief:
- Extract available information
- Note in email: "Based on our brief conversation..."
- Still generate full email structure

### Multiple Recipients
IF transcript includes 3+ external participants:
- Ask: "Who is the primary recipient? Options: [names]"
- Use "Hi [Company] team," if addressing multiple people

### No Clear Action Items
IF no explicit action items mentioned:
- Don't invent tasks
- Focus on "Great connecting with you" + resources shared
- Offer: "Let me know if you'd like to discuss next steps"

### Transcript Has Speaker Labels Without Names
IF transcript shows "Speaker 1", "Speaker 2" instead of names:
- Extract content anyway
- Ask user: "Can you tell me who the participants were?"
- Use [NAME] placeholders until clarified
</edge_cases>
```

**Why this works:**
- Main instructions stay under 500 lines (maintainable)
- Edge cases documented but not intrusive
- Claude only processes edge case logic when needed
- Scales better than trying to cover everything upfront

**Source verification:** Official Anthropic guidance states "Skills should start simple and add complexity progressively" and "Conciseness is key—Claude is already smart."

### Anti-Patterns to Avoid

- **Strict keyword matching for intent detection:** Don't require exact phrases like "/follow-up-email". Claude handles flexible phrasing natively.
- **Blocking generation on questions:** Don't wait for user responses before generating draft. Ask questions, generate anyway, offer to regenerate.
- **Dynamic resource search:** Don't try to search Postman docs in real-time. Curated library in prompt is more reliable and faster.
- **Regex for attachment detection:** Don't write complex regex patterns. Claude's semantic understanding handles variations better.
- **Over-asking questions:** Don't ask about every ambiguous detail. Only ask for critical missing info (recipient name, ambiguous action items).

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| Flexible intent detection | Pattern matching or NLP libraries | Claude's native intent inference with contextual examples | Claude 4.5 understands "Did they mean to invoke this skill?" from context without special libraries |
| Handling missing entity data | Custom validation and error handling | Explicit placeholder instructions in prompt | Claude follows "use [PLACEHOLDER] for missing data" instructions reliably |
| Resource recommendation engine | Vector search, RAG pipeline, external API | Curated resource library embedded in prompt | For bounded domains (Postman docs), static list in prompt is faster, more accurate, zero infrastructure |
| Keyword detection for attachments | Regex patterns, spaCy NER models | Claude's semantic understanding with keyword guidance | Natural language variations ("I'll get you that deck" vs "I'll send the slides") handled natively |
| Conditional logic orchestration | State machines, decision trees, external orchestration | XML-structured prompts with IF/THEN logic | Claude 4.5 follows explicit conditional instructions reliably |
| Clarifying question management | Custom dialog management framework | Claude's native conversation capabilities with explicit ask protocol | Built-in multi-turn context awareness sufficient |

**Key insight:** Phase 2 adds complexity (conditional logic, resource matching, attachment detection) but still doesn't require infrastructure. Claude 4.5's native capabilities—intent understanding, entity extraction with fallbacks, contextual reasoning—handle everything. The skill author's job remains precise prompting with well-structured instructions.

## Common Pitfalls

### Pitfall 1: Over-Reliance on Strict Keywords for Activation

**What goes wrong:** Skill requires exact phrases ("generate follow-up email") and misses valid intent expressed differently ("Can you help me write to this customer?")
**Why it happens:** Traditional software mindset of exact pattern matching
**How to avoid:** Provide examples of valid invocation patterns, let Claude infer intent from context
**Warning signs:** Users saying "it didn't work" when they clearly intended to use the skill

**Prevention strategy:**
```xml
<activation_detection>
## Invocation Examples

User clearly wants skill activated:
- "Generate a follow-up email from this transcript"
- "Draft an email for this call"
- "Help me write a follow-up" + [transcript]
- "Use the skill on this Gong transcript"

User intent is ambiguous:
- "What should I do with this?" + [transcript]
- "Help me with this call"

User did NOT invoke skill:
- Just pasting transcript without any request
- Asking about Gong features or transcript content

When in doubt, ask: "Did you want me to generate a follow-up email?"
</activation_detection>
```

**Claude handles the fuzzy matching natively—no need for regex or exact keywords.**

### Pitfall 2: Blocking Draft Generation on Clarifying Questions

**What goes wrong:** Skill asks "Who is the recipient?" and waits for answer before generating anything
**Why it happens:** Misunderstanding user expectation—"asking for more is additive, not blocking"
**How to avoid:** Always generate a draft immediately. Ask questions for refinement, not prerequisites.
**Warning signs:** Users frustrated by "it's asking me questions instead of giving me a draft"

**Prevention strategy:**
```xml
<clarification_protocol>
CRITICAL RULE: Always generate a draft email, even if information is missing.

Questions are for IMPROVEMENT, not BLOCKING.

## Workflow

1. Extract available information (even if incomplete)
2. Generate draft email with [PLACEHOLDER] for missing data
3. THEN ask clarifying questions
4. Offer to regenerate with new information

NEVER wait for answers before generating initial draft.
</clarification_protocol>
```

**User expectation from CONTEXT.md:** "Always generate a draft no matter what—asking for more is additive, not blocking."

### Pitfall 3: Attempting Dynamic Resource Search

**What goes wrong:** Skill tries to search Postman documentation in real-time, hits API rate limits, retrieves irrelevant results, or hallucinates URLs
**Why it happens:** Instinct that "dynamic search is more comprehensive than static list"
**How to avoid:** Embed curated resource library directly in prompt. Postman docs are stable and bounded.
**Warning signs:** Considering RAG pipeline, vector database, or Postman API integration

**Why static library wins:**
- Instant (no API calls)
- Accurate (no hallucinated URLs)
- Curated (quality over quantity)
- Zero infrastructure
- Maintainable (update library in SKILL.md when Postman adds major new docs)

**When dynamic search would make sense:** If Postman had thousands of rapidly changing documentation pages. Reality: core resources are stable, comprehensive list fits in prompt.

**Source verification:** Research on [RAG for LLM knowledge bases](https://slite.com/en/learn/llm-knowledge-base) confirms "clean and curated RAG sources are critical" and for bounded domains, static curation outperforms dynamic retrieval.

### Pitfall 4: False Positives on Attachment Reminders

**What goes wrong:** Email includes "[ATTACH: deck]" when speaker only mentioned "the deck we saw last week" (referenced but not promised)
**Why it happens:** Triggering on any presentation keyword without checking sharing context
**How to avoid:** Require BOTH presentation words AND sharing/sending words
**Warning signs:** Placeholders appearing when nothing was actually promised

**Prevention strategy:**
```xml
<attachment_detection>
## Two-Condition Rule

Attachment placeholder ONLY when BOTH conditions met:

1. PRESENTATION word ("deck", "slides", "PDF", etc.)
2. SHARING word ("send", "share", "attach", "forward", etc.)

EXAMPLES:

"I'll send the deck" → YES (presentation + sharing)
"The deck from last week" → NO (only presentation, no sharing promise)
"I'll share those slides" → YES (presentation + sharing)
"We looked at the roadmap" → NO (no sharing promise)

When in doubt, check: Did speaker PROMISE to send something?
</attachment_detection>
```

**Source:** User decision in CONTEXT.md specifying "trigger keywords: both presentation words AND sharing words."

### Pitfall 5: Overwhelming Resource Suggestions

**What goes wrong:** Email includes 15+ Postman documentation links, drowning out the main message
**Why it happens:** "More is better" instinct—include every possibly relevant resource
**How to avoid:** Prioritize directly relevant links (1-3), group "also useful" resources separately (4-7 max)
**Warning signs:** Emails feel like documentation dumps rather than follow-ups

**Prevention strategy:**
```xml
<resource_matching>
## Suggestion Guidelines

DIRECTLY RELEVANT (inline in email body): 1-3 links
- Topics explicitly discussed during call
- Resources mentioned by name
- Solutions to specific problems raised

ALSO USEFUL (optional grouped section): 4-7 links
- Related topics not directly discussed
- Adjacent features that might help
- General Postman resources

OMIT:
- Tangentially related topics
- Resources already in customer's knowledge base
- Links to general documentation home pages

Quality over quantity. Better to suggest 3 perfect resources than 10 maybe-relevant ones.
</resource_matching>
```

**User guidance from CONTEXT.md:** "No hard limit on links—typically 3-7 based on call content." This prevents both over-linking and artificial restrictions.

### Pitfall 6: Losing Natural Email Tone with Complexity

**What goes wrong:** Enhanced skill generates robotic emails: "Regarding action item 1 mentioned in transcript at timestamp 00:15:22..."
**Why it happens:** Adding structure (XML tags, conditional logic, resource matching) can make output feel mechanical
**How to avoid:** Keep few-shot examples from Phase 1. Reference natural tone throughout instructions.
**Warning signs:** Emails feel AI-generated, lack warmth, use formal language where friendly would work better

**Prevention strategy:**
- Maintain few-shot examples from Phase 1 (real emails showing natural tone)
- Explicitly remind: "Generate natural, conversational follow-up—match tone of examples"
- Don't number action items unless there are 4+
- Use inline resource mentions ("I mentioned the verification checklist") not lists unless 3+
- Test generated emails against Phase 1 MVP quality

**From Phase 1 CONTEXT decisions:** "Professional friendly—match examples in follow_up_emails_agentic_context.md." This tone standard carries into Phase 2.

## Code Examples

### Example 1: Enhanced SKILL.md Frontmatter (Unchanged)

```yaml
---
name: gong-follow-up-email
description: Generate professional follow-up emails from Gong sales call transcripts. Use when user pastes a transcript and needs a ready-to-send follow-up email with extracted participants, action items, and resources. Handles incomplete transcripts gracefully and suggests relevant Postman documentation links.
---
```

**Why description is enhanced:** Added "Handles incomplete transcripts gracefully and suggests relevant Postman documentation links" to reflect Phase 2 capabilities.

### Example 2: Intent Detection with Flexible Activation

```xml
<activation_detection>
# Detecting User Intent

You must only activate this skill when the user clearly indicates intent to generate a follow-up email.

## Clear Invocation (Proceed Immediately)

Examples of clear intent:
- "Generate a follow-up email from this transcript"
- "Draft an email based on this Gong call"
- "Use the skill to create follow-up" + [transcript]
- "Help me write a follow-up email" + [transcript]
- Pasting transcript + "Make this into an email"

Key: User explicitly mentions "skill", "follow-up", "email", or "draft" + provides transcript.

## Ambiguous Intent (Ask for Confirmation)

User says something like:
- "What should I do with this?" + [transcript]
- "Help me with this call"
- "Process this transcript"

Response: "Did you want me to generate a follow-up email from this transcript?"

## No Invocation (Suggest Usage)

User pastes transcript WITHOUT requesting email generation.

Response: "I see you've shared a transcript. Would you like me to draft a follow-up email from it?"

## Optimized for Gong, Handles Any Format

Primary use case: Gong transcripts with speaker labels and timestamps
Also works with: Any conversational text, meeting notes, bullet point summaries

Extract what's available, generate email from any input format provided.
</activation_detection>
```

**Source:** Combines user decisions from CONTEXT.md with [Claude's intent inference capabilities](https://www.anthropic.com/engineering/claude-code-best-practices).

**Why this works:**
- Concrete examples show Claude what "clear intent" looks like
- Three-tier decision tree (clear / ambiguous / none) covers all cases
- Natural language descriptions, not regex patterns
- Explicitly handles user expectation: "not a shibboleth" (doesn't require exact wording)

### Example 3: Ask-Then-Generate Protocol

```xml
<clarification_protocol>
# When and How to Ask Questions

Questions are ADDITIVE, not BLOCKING. Always generate a draft regardless.

## Recipient Name Unclear

IF recipient cannot be confidently identified from transcript:

1. Generate draft email with [NAME] placeholder in greeting
2. Ask user: "Who is the recipient for this email? I found these people in the transcript: [list names with roles]"
3. Offer: "Reply with the name and I'll regenerate with the correct greeting"

NEVER block draft generation waiting for recipient name.

## Action Items Ambiguous

IF action items have multiple interpretations or unclear ownership:

1. Generate draft with all plausible action items
2. Use [OWNER] placeholder for unclear assignments
3. Ask user: "I found these possible action items—are all of these correct?"
   [List extracted items with ambiguity notes]
4. Offer: "Let me know if I should add, remove, or clarify any"

NEVER omit action items because they're ambiguous—include with placeholders.

## Transcript Seems Incomplete

IF transcript appears cut off, very brief, or missing context:

1. Generate draft immediately based on available information
2. Note limitations in your response: "This transcript seems incomplete. I've generated a draft based on what's here."
3. Ask: "Would you like to provide more context or a longer transcript?"
4. Offer: "I can regenerate if you have additional information"

NEVER block generation saying "need more information."

## General Principle

User wants draft FIRST, refinement SECOND. Always deliver a complete email, then offer to improve it with clarifications.
</clarification_protocol>
```

**Source:** User decisions in CONTEXT.md ("always generate a draft no matter what") + [Claude AskUserQuestion patterns](https://www.atcyrus.com/stories/claude-code-ask-user-question-tool-guide).

**Why this works:**
- Explicit IF/THEN structure maps to Claude 4.5's instruction following
- "NEVER block" statements prevent common mistake
- Concrete phrasing for questions maintains friendly tone
- "Offer to regenerate" pattern enables iterative refinement

### Example 4: Embedded Resource Library with Contextual Matching

```xml
<resource_library>
# Postman Documentation Resources

Curated library for contextual suggestions based on call topics.

## Verification & Publishing
- **Verification Checklist**: https://www.postman.com/publisher/settings/verification
  Topics: publishing, verification, public API, submit to network
- **Public API Network**: https://www.postman.com/explore
  Topics: discover, explore, public APIs, showcase

## Documentation & Notebooks
- **Creating Notebooks**: https://learning.postman.com/docs/publishing-your-api/creating-notebooks/
  Topics: documentation, notebooks, markdown, visualizations
- **Visualizer**: https://learning.postman.com/docs/sending-requests/visualizer/
  Topics: visualize responses, charts, data display
- **Collection Documentation**: https://learning.postman.com/docs/publishing-your-api/documenting-your-api/
  Topics: document APIs, auto-generate docs

## Testing & Automation
- **Writing Tests**: https://learning.postman.com/docs/writing-scripts/test-scripts/
  Topics: testing, assertions, scripts, validation
- **Newman CLI**: https://learning.postman.com/docs/running-collections/using-newman-cli/
  Topics: automation, CI/CD, command line, continuous integration
- **Collection Runs**: https://learning.postman.com/docs/running-collections/intro-to-collection-runs/
  Topics: batch testing, run multiple requests, data-driven

## Collaboration
- **Workspaces**: https://learning.postman.com/docs/collaborating-in-postman/using-workspaces/
  Topics: team collaboration, shared workspace, organize work
- **Comments & Tagging**: https://learning.postman.com/docs/collaborating-in-postman/working-with-your-team/commenting/
  Topics: feedback, reviews, team communication

## Run in Postman
- **Run in Postman Button**: https://learning.postman.com/docs/publishing-your-api/run-in-postman/introduction-run-button/
  Topics: embed, share collections, Run in Postman, integrate

## Enterprise
- **SSO Configuration**: https://learning.postman.com/docs/administration/sso/intro-sso/
  Topics: single sign-on, authentication, enterprise security
- **SCIM Provisioning**: https://learning.postman.com/docs/administration/scim-provisioning/
  Topics: user management, provisioning, enterprise admin
</resource_library>

<resource_matching>
# Contextual Resource Suggestions

Based on topics extracted from transcript, suggest relevant Postman resources.

## Matching Logic

Review topics discussed in call. Match to resources using topic keywords above.

DIRECTLY RELEVANT (include inline in email, 1-3 links):
- Topics explicitly discussed during call
- Problems mentioned that resource solves
- Features demonstrated or explained

ALSO USEFUL (grouped section if 4+ total links):
- Adjacent topics likely helpful
- Related features mentioned briefly
- General resources for their use case

## Formatting in Email

INLINE (for 1-3 directly relevant resources):
"As we discussed, the [verification checklist](https://www.postman.com/publisher/settings/verification) walks through the publishing process."

GROUPED (for 4-7 resources):
"Here are the resources we discussed:
- [Verification checklist](url) for publishing your API to the Public API Network
- [Creating Notebooks](url) to build interactive documentation
- [Run in Postman button](url) to let users import your collection directly"

Use descriptive anchor text from conversation, not generic "click here" or bare URLs.

## Quality Guidelines

- Better to suggest 3 perfect resources than 7 maybe-relevant ones
- Only include resources actually mentioned or directly solving discussed problems
- Don't suggest general Postman homepage or learning center—be specific
- Match conversation level: if discussing advanced features, link to advanced docs
</resource_matching>
```

**Source:** Combines [context-aware recommendation systems research](https://link.springer.com/article/10.1007/s10462-024-10939-4) with user decisions on link formatting.

**Why this works:**
- Curated library ensures no hallucinated URLs
- Topic keywords enable simple keyword-based matching (no vector search needed)
- Quality guidelines prevent over-linking
- Format examples show both inline and grouped presentation
- Maintainable: add new resources to library as Postman docs expand

### Example 5: Semantic Attachment Detection

```xml
<attachment_detection>
# Attachment Reminder Logic

Add attachment placeholders when user promises to send materials.

## Two-Condition Rule

Attachment reminder ONLY when transcript contains BOTH:

1. **PRESENTATION words**: deck, slides, presentation, PDF, document, file, materials, spreadsheet
2. **SHARING words**: send, share, attach, forward, email you, get you, provide

## Detection Examples

"I'll send over the onboarding deck" → YES
  Presentation: "deck" | Sharing: "send over"
  Placeholder: [ATTACH: onboarding deck]

"Let me share those slides with you" → YES
  Presentation: "slides" | Sharing: "share"
  Placeholder: [ATTACH: slides]

"The deck from last week showed..." → NO
  Presentation: "deck" | Sharing: NONE (just referenced, not promised)

"I'll send you the quarterly results" → YES
  Presentation: implied document | Sharing: "send you"
  Placeholder: [ATTACH: quarterly results]

"We looked at the roadmap" → NO
  Presentation: implied document | Sharing: NONE

## Semantic Understanding

Use Claude's natural language understanding—don't require exact keyword matches.

Variations that count as SHARING words:
- "I'll get you that" (implies sending)
- "Forward the materials" (forwarding is sharing)
- "Email you the file" (explicit sharing)
- "You'll receive the deck" (implies sending)

## Placeholder Format

Name attachment based on context from transcript:

"I'll share the integration guide" → [ATTACH: integration guide]
"Sending over the Q3 slides" → [ATTACH: Q3 slides]
"Attaching the customer deck" → [ATTACH: customer deck]

## Placement in Email

Place placeholder where it fits naturally:

INLINE: "I'll send the [ATTACH: onboarding deck] by end of day."
SEPARATE LINE: "[ATTACH: quarterly review slides]"
WITH ACTION ITEM: "• Send [ATTACH: integration guide] to team"

## Multiple Attachments

If multiple items promised, create separate placeholder for each:

"I'll send the deck and the case study"
→ [ATTACH: deck]
→ [ATTACH: case study]
</attachment_detection>
```

**Source:** User decisions in CONTEXT.md + [NOVA framework for semantic pattern matching](https://github.com/fr0gger/nova-framework).

**Why this works:**
- Two-condition rule prevents false positives (mentioned but not promised)
- Semantic understanding handles natural variations ("I'll get you" = sharing)
- Context-aware naming more useful than generic "[ATTACHMENT]"
- Examples show edge cases clearly
- Flexible placement maintains natural email flow

## State of the Art

| Aspect | Current Approach (2026) | Previous Approach | Impact |
|--------|-------------------------|-------------------|--------|
| Intent detection | Claude 4.5 native intent inference with contextual examples | Regex patterns, keyword matching, custom NLP models | Handles flexible phrasing, no brittle patterns |
| Entity extraction with missing data | Explicit placeholder instructions + fallback patterns | Validation libraries, error handling, strict schemas | Graceful degradation, always generates output |
| Conditional logic | XML-structured prompts with IF/THEN instructions | External orchestration frameworks, state machines | Zero infrastructure, maintainable in single file |
| Resource recommendation | Curated knowledge base embedded in prompt | RAG pipelines, vector search, dynamic retrieval | Instant, accurate, no hallucination risk |
| Keyword detection | Semantic pattern matching via LLM understanding | Regex, spaCy NER, traditional NLP libraries | Handles natural language variations |
| Clarifying questions | Built-in Claude conversation capabilities | Custom dialog management frameworks | Works natively, no state management needed |

**Key Shift:** From "building systems to handle complexity" to "structuring prompts to leverage LLM capabilities." Claude 4.5's native strengths—intent inference, contextual reasoning, semantic understanding—eliminate need for external tools even as skill complexity increases.

**Deprecated/outdated approaches:**
- **Strict keyword matching for activation:** Claude handles flexible intent natively
- **Blocking workflows:** Modern LLM skills should be conversational and iterative, not sequential
- **Dynamic resource search for bounded domains:** Static curated lists are faster and more accurate
- **Regex for natural language patterns:** LLMs understand semantic variations better
- **External state management:** Claude's context window handles multi-turn refinement

## Open Questions

### Question 1: Threshold for "Unclear Invocation" vs. "Clear Invocation"

**What we know:** User wants flexible activation ("not a shibboleth") but not auto-activation (must clearly indicate intent)
**What's unclear:** Exact boundary cases—e.g., "What should I do with this call?" + transcript
**Recommendation:** Start permissive (treat borderline cases as "ambiguous, ask for confirmation"). Iterate based on user feedback. If false positives (unwanted activation), tighten. If false negatives (skill doesn't activate when wanted), loosen.

**Confidence:** MEDIUM—will require real-world testing to calibrate

### Question 2: Optimal Depth of Postman Resource Library

**What we know:** Curated list in prompt works for bounded domain
**What's unclear:** Whether 15 resources sufficient or should expand to 30-40
**Recommendation:** Start with 15-20 most commonly discussed resources (verification, notebooks, Run in Postman, testing, enterprise features). Monitor generated emails—if skill frequently says "no relevant resources found" for valid topics, expand library. If library grows beyond ~40 resources, consider categorization or retrieval strategy.

**Confidence:** HIGH for initial set, MEDIUM for expansion criteria

### Question 3: Handling Very Technical vs. Executive Calls

**What we know:** Email tone should be "professional friendly" per Phase 1 decisions
**What's unclear:** Whether skill should adapt tone based on call content (e.g., technical deep-dive vs. executive overview)
**Recommendation:** Keep consistent tone (professional friendly) for Phase 2. If users request tone variations, can add Phase 3 enhancement: detect call type, adjust formality. For now, one tone fits most sales follow-ups.

**Confidence:** HIGH—consistent tone is safer than auto-varying tone which could miss context

### Question 4: Resource Suggestion Confidence Scoring

**What we know:** Match topics to resources using keyword-based logic
**What's unclear:** Whether to include confidence indicators ("This might be helpful: [link]" vs "Here's the resource we discussed: [link]")
**Recommendation:** Use confident language for directly mentioned resources, softer language for adjacent suggestions. Example:
- Direct: "As discussed, the [verification checklist](url)..."
- Adjacent: "You might also find the [Newman CLI guide](url) helpful for automation."

No need for explicit confidence scores—natural language phrasing sufficient.

**Confidence:** HIGH—natural phrasing clearer than scores

### Question 5: Multi-Call Transcript Handling

**What we know:** Skill designed for single call transcripts
**What's unclear:** What happens if user pastes transcripts from multiple calls
**Recommendation:** Out of scope for Phase 2. If detected (multiple date headers, non-sequential timestamps), respond: "This looks like multiple call transcripts. Please paste one transcript at a time for best results."

**Confidence:** HIGH—multi-call is Phase 3+ feature if needed

## Sources

### Primary (HIGH confidence)

**Official Anthropic Documentation:**
- [Use XML tags to structure prompts - Claude Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/use-xml-tags) - XML structuring for complex prompts, conditional logic, nested tags
- [Be clear, direct, and detailed - Claude Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/be-clear-and-direct) - Explicit instructions, sequential steps, contextual information
- [Prompt engineering overview - Claude Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/overview) - Core techniques ordered by effectiveness
- [Skill authoring best practices - Claude Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices) - Progressive disclosure, conciseness, avoiding over-specification

**Project Context:**
- `.planning/phases/02-robust-handling/02-CONTEXT.md` - User decisions on activation, incomplete handling, resource suggestions, attachment reminders
- `.planning/phases/01-single-file-mvp/01-RESEARCH.md` - Phase 1 findings on SKILL.md structure, few-shot prompting, natural tone

### Secondary (MEDIUM confidence)

**Verified Web Research:**

**Intent Recognition & Prompt Engineering:**
- [Claude Code Best Practices (Anthropic)](https://www.anthropic.com/engineering/claude-code-best-practices) - Intent inference, handling ambiguity, asking questions
- [Claude Prompt Engineering Best Practices 2026](https://promptbuilder.cc/blog/claude-prompt-engineering-best-practices-2026) - 4-block pattern, structured prompts, uncertainty handling
- [Prompt Engineering Guide 2026 (Analytics Vidhya)](https://www.analyticsvidhya.com/blog/2026/01/master-prompt-engineering/) - Graceful degradation, error handling, modality-specific strategies

**Entity Extraction & Missing Data:**
- [Structured Data Extraction with LLMs (LlamaIndex)](https://docs.llamaindex.ai/en/stable/use_cases/extraction/) - Schema design, temperature settings, example-based learning, preventing hallucinations
- [Anthropic Cookbook - Extracting Structured JSON](https://github.com/anthropics/anthropic-cookbook/blob/main/tool_use/extracting_structured_json.ipynb) - Tool use for structured output, handling incomplete data
- [AWS Blog - Prompt Engineering Techniques with Claude 3](https://aws.amazon.com/blogs/machine-learning/prompt-engineering-techniques-and-best-practices-learn-by-doing-with-anthropics-claude-3-on-amazon-bedrock/) - Missing data handling: "output empty JSON object '{}' if no information can be extracted"

**Clarifying Questions:**
- [Claude Code AskUserQuestion Tool Guide](https://www.atcyrus.com/stories/claude-code-ask-user-question-tool-guide) - When to ask, how to phrase questions, non-blocking patterns
- [Handle approvals and user input - Claude Docs](https://platform.claude.com/docs/en/agent-sdk/user-input) - Claude's built-in user input handling
- [How Claude Code Handles Ambiguous Instructions](https://milvus.io/ai-quick-reference/how-does-claude-code-handle-ambiguous-instructions) - Asking clarifying questions vs making assumptions

**Resource Recommendation & Knowledge Bases:**
- [Context-Aware Recommender Systems Review (Springer)](https://link.springer.com/article/10.1007/s10462-024-10939-4) - Contextual attributes, multi-type context fusion, temporal/spatial components
- [LLM Knowledge Base Guide (Slite)](https://slite.com/en/learn/llm-knowledge-base) - Curated sources vs dynamic search, clean RAG sources
- [Top 5 LLM Prompts for RAG (Scout)](https://www.scoutos.com/blog/top-5-llm-prompts-for-retrieval-augmented-generation-rag) - Clean curated sources critical, mismatched docs lead model astray
- [Context Engineering Guide (Prompt Engineering Guide)](https://www.promptingguide.ai/guides/context-engineering-guide) - Context retrieval and generation, dynamic context assembly

**Keyword Detection:**
- [NOVA Framework - Prompt Pattern Matching (GitHub)](https://github.com/fr0gger/nova-framework) - Combining keyword detection, semantic similarity, and LLM matching
- [Regex Cheatsheet for NLP (Analytics Vidhya)](https://www.analyticsvidhya.com/blog/2021/06/regex-cheatsheet-for-natural-language-processing-tasks/) - Pattern matching basics, but LLM semantic understanding superior

**Gong Transcript Structure:**
- [Gong - View a Call Transcript](https://help.gong.io/docs/view-a-call-transcript) - Speaker identification, timestamps, multi-language support
- [Gong Note Taker Review 2026 (Lindy)](https://www.lindy.ai/blog/gong-note-taker) - Smart Brief format: Problem/Solution/Next Steps
- [Can You Export a Call Transcript? (Gong Community)](https://visioneers.gong.io/configuring-gong-76/can-you-export-a-call-transcript-520) - Copy-paste format: speaker and transcribed audio in separate columns

### Tertiary (LOW confidence - informational only)

- [Mastering Claude AI in 2026 (Amazon)](https://www.amazon.com/Mastering-Claude-2026-Engineering-Entrepreneurs-ebook/dp/B0G3X2TWWV) - Third-party guide, unverified content
- Various web search results on embedding models, vector search, semantic similarity (informational context, not directly used)

## Metadata

**Confidence breakdown:**
- **Intent detection patterns: HIGH** - Based on official Claude documentation and verified real-world usage patterns
- **XML-structured conditional logic: HIGH** - Direct from Anthropic's official prompt engineering best practices
- **Entity extraction with missing data: HIGH** - Verified through official cookbook examples and LLM extraction research
- **Ask-then-generate protocol: HIGH** - Aligns with user decisions and Claude's native conversation capabilities
- **Embedded resource library: HIGH** - Curated approach proven for bounded domains, well-researched
- **Semantic attachment detection: MEDIUM** - Pattern is sound (two-condition rule), but calibration may need refinement based on real transcripts
- **Threshold for ambiguous invocation: MEDIUM** - Will require real-world testing to calibrate sensitivity
- **Optimal resource library size: MEDIUM** - Starting set is solid, expansion criteria need validation

**Research date:** 2026-01-21
**Valid until:** ~30 days (Claude prompt engineering practices stable; skill format stable; Postman documentation relatively stable)
**Recommended re-research trigger:** If users report frequent false positives/negatives on activation detection, if attachment reminders consistently miss or over-trigger, if resource suggestions are frequently off-target
