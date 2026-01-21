# Publisher Follow-up Email Skill

## What This Is

A Claude skill that generates professional follow-up emails for Postman Publisher Success managers after publisher calls. Given a Gong transcript, the skill extracts action items, identifies resources mentioned, and drafts a ready-to-send email following the team's established format conventions.

## Core Value

Transform call transcripts into polished, consistent follow-up emails with minimal manual effort — ensuring no action items or resources slip through the cracks.

## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] Accept Gong transcript as input (copy/paste into conversation)
- [ ] Extract recipient names/roles from transcript when possible, otherwise use placeholders
- [ ] Generate greeting with thanks and meeting acknowledgment
- [ ] Extract and list resources/links mentioned during the call (as hyperlinked text, not raw URLs)
- [ ] Suggest relevant standard Postman links based on context (verification checklist, notebooks, Run in Postman, etc.)
- [ ] Extract and list next steps/action items discussed
- [ ] Smart reminder about attachments when deck/PDF sharing was mentioned
- [ ] Configurable signature (default: "Best,\nDavid")
- [ ] Output ready-to-paste email when all info available, placeholders when gaps exist
- [ ] Work in both Claude Desktop (primary) and Claude Code
- [ ] Package as ZIP for easy team distribution
- [ ] Include setup instructions for team members

### Out of Scope

- Automatic email sending — output is copy/paste only
- Integration with Gmail/Outlook — manual paste into email client
- Automatic transcript fetching from Gong — user copy/pastes transcript
- CRM integration — no Salesforce/HubSpot connectivity
- Meeting scheduling — no calendar integration for proposed follow-ups

## Context

The Publisher Success team at Postman helps API publishers on the Postman API Network find success. After publisher calls, the team sends follow-up emails that follow a consistent format:

1. Greeting + thanks for the call
2. Resources/links discussed (hyperlinked text)
3. Next steps / action items
4. Offer to help / propose next check-in
5. Sign-off

These emails often include standard Postman resources (verification checklist, notebooks examples, Run in Postman button docs, workspace updates, Publisher Analytics, etc.) that the skill should be aware of and suggest contextually.

The team mostly uses Claude Desktop. David also uses Claude Code via work API key. The skill needs to work in both environments with identical behavior.

## Constraints

- **Platform compatibility**: Must work in both Claude Desktop (ZIP upload) and Claude Code (~/.claude/skills/)
- **No code execution required**: Skill should work with just Skill.md instructions (no Python/JS dependencies)
- **Account separation**: David's Claude Code uses work API, Claude Desktop uses personal Max — skill file itself is just text/instructions, no credential concerns
- **Team distribution**: Must be shareable as a ZIP file with setup instructions for non-technical team members

## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| Claude Desktop as primary target | Most of the team uses Desktop, David is the only Claude Code user | — Pending |
| Hyperlinked text over raw URLs | Cleaner email format, matches existing email style | — Pending |
| Configurable signature | Team members have different names | — Pending |
| Resource library built into skill | Contextual suggestions reduce manual link hunting | — Pending |

---
*Last updated: 2025-01-21 after initialization*
