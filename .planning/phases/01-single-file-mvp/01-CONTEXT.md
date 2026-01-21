# Phase 1: Single-File MVP - Context

**Gathered:** 2026-01-21
**Status:** Ready for planning

<domain>
## Phase Boundary

Transform Gong transcripts into ready-to-send follow-up emails via a Claude SKILL.md file. User pastes transcript, receives professional email with extracted participants, action items, and resources. The skill must work as a single file — no external dependencies.

</domain>

<decisions>
## Implementation Decisions

### Email Tone & Structure
- Tone: Professional friendly — match examples in `follow_up_emails_agentic_context.md`
- Opening: Auto-adapt timing ("today/yesterday/on [day]") based on transcript context
- Section order: Context-dependent — order resources vs next steps based on what was emphasized in the call
- Next steps section: Include only when action items were discussed (but expect this for most follow-up emails)
- Closing: Warm and helpful ("Happy to help with any questions!" / "Let us know if any questions come up")

### Action Item Formatting
- Format: Bullet list for 3+ items; inline prose acceptable for fewer items or when bullets don't fit naturally
- Ownership: Attribute to specific person only when explicitly clear from transcript
- Timelines: Include if mentioned; use [TIMELINE] placeholder when timing is unclear
- Grouping: Claude's discretion based on number/type of items

### Resource Linking
- Link style: Hyperlinked text (e.g., `[verification checklist](url)`) — NOT raw URLs
- Missing URLs: Use `https://placeholder` as URL so hyperlink structure remains for manual editing
- Organization: Flat list preferred; nest with sub-bullets only when necessary to convey concepts
- Descriptions: Add brief context only when not obvious from the link text itself

### Name Handling
- Greeting: First name for single recipient ("Hi Vincent,"); team greeting for multiple ("Hi [Company] team,")
- Missing names: Ask user to provide name before generating — no placeholders for recipient greeting
- Teammates (CC'd): First name if previously mentioned in thread; @First Last format (Gmail @-mention style) if new introduction
- External contacts: First name in action items and body text

### Claude's Discretion
- Exact wording of opening pleasantry ("Nice to meet you" vs "Great chatting")
- Whether to include "Please see attached" line (based on deck/PDF mentions)
- Paragraph vs bullet formatting for short lists
- Action item grouping strategy

</decisions>

<specifics>
## Specific Ideas

- Reference file: `follow_up_emails_agentic_context.md` contains 14 real email examples with the exact tone and structure to emulate
- Inferred format from examples:
  ```
  Hi {Name/Team},

  Thanks for your time {today/yesterday}! {Nice to meet you / great chatting}.

  Following up with {the deck/resources} from our call:
  - {Resource 1}
  - {Resource 2}

  Next steps (as discussed):
  - {Next step 1}
  - {Next step 2}

  {Offer help / propose next check-in}

  Best,
  {Signature}
  ```
- Signature is configurable (default: "Best,\nDavid")

</specifics>

<deferred>
## Deferred Ideas

- Resource library with commonly used Postman links — Phase 2
- Auto-activation when transcript is pasted — Phase 2
- Attachment reminder for deck/PDF mentions — Phase 2

</deferred>

---

*Phase: 01-single-file-mvp*
*Context gathered: 2026-01-21*
