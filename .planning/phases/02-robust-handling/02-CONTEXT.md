# Phase 2: Robust Handling - Context

**Gathered:** 2026-01-21
**Status:** Ready for planning

<domain>
## Phase Boundary

Make the existing follow-up email skill resilient to real-world input variations and smarter about resource suggestions. The skill should handle incomplete transcripts gracefully, suggest relevant Postman resources contextually, and remind about attachments when promised in the call.

</domain>

<decisions>
## Implementation Decisions

### Activation Triggers
- Explicit invocation required — user must clearly indicate they want to use the skill
- Flexible phrasing between "explicit mention" and "strict" — must mention "skill" or "follow-up" but doesn't need exact wording
- Optimized for Gong transcripts but handles whatever context is provided
- If invocation seems intended but unclear: ask "Did you want me to generate a follow-up email?"
- If transcript pasted WITHOUT invocation: suggest "Would you like me to draft a follow-up email from this transcript?"

### Incomplete Transcript Handling
- When recipient name unclear: ask before generating — "Who is the recipient?"
- When action items ambiguous: ask for clarification — "I found these possible action items — which should I include?"
- When transcript seems incomplete: ask for more context, but still generate a draft regardless
- Always generate a draft no matter what — asking for more is additive, not blocking

### Resource Suggestions
- Auto-include relevant links directly into email draft
- No hard limit on links — typically 3-7 based on call content
- Curated Postman resource library as primary source, supplement with search if topic not covered
- Format: flexible based on context — inline for directly relevant links, grouped section for "also useful" resources

### Attachment Reminders
- Trigger keywords: both presentation words ("deck", "slides", "presentation", "PDF") AND sharing words ("send over", "share", "attached", "I'll send")
- Placeholder appears in email draft where it fits naturally
- Format: context from call — "[ATTACH: onboarding deck]" naming what was mentioned
- Multiple attachments: separate placeholder for each item

### Claude's Discretion
- Exact threshold for "unclear invocation" vs "clear invocation"
- Which specific resources to include from library based on call context
- Inline vs grouped placement of resource links based on email flow
- Natural placement of attachment placeholders within email

</decisions>

<specifics>
## Specific Ideas

- "It shouldn't have to be verbatim, it's not a shibboleth" — activation should recognize intent without requiring exact phrasing
- User prefers asking questions to get things right over making assumptions
- Draft generation should never be blocked — questions are additive

</specifics>

<deferred>
## Deferred Ideas

None — discussion stayed within phase scope

</deferred>

---

*Phase: 02-robust-handling*
*Context gathered: 2026-01-21*
