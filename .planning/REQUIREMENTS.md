# Requirements: Publisher Follow-up Email Skill

**Defined:** 2025-01-21
**Core Value:** Transform call transcripts into polished, consistent follow-up emails with minimal manual effort

## v1 Requirements

Requirements for initial release. Each maps to roadmap phases.

### Core Extraction

- [ ] **EXTR-01**: Skill accepts Gong transcript as copy/paste input
- [ ] **EXTR-02**: Skill extracts participant names and roles from transcript
- [ ] **EXTR-03**: Skill identifies action items / next steps discussed
- [ ] **EXTR-04**: Skill extracts resources and URLs mentioned during call

### Email Generation

- [ ] **GEN-01**: Skill generates professional greeting with thanks for the call
- [ ] **GEN-02**: Skill formats resources as hyperlinked text (not raw URLs)
- [ ] **GEN-03**: Skill generates formatted next steps / action items section
- [ ] **GEN-04**: Skill reminds about attachments if deck/PDF sharing was mentioned
- [ ] **GEN-05**: Skill generates appropriate closing and sign-off

### Configuration

- [ ] **CONF-01**: Signature is configurable (default: "Best,\nDavid")
- [ ] **CONF-02**: Skill includes Postman resource library for contextual suggestions
- [ ] **CONF-03**: Skill uses [PLACEHOLDER] markers for missing information

### Delivery

- [ ] **DLVR-01**: Output is ready-to-paste text (no HTML/rich text complexity)
- [ ] **DLVR-02**: Output adapts structure based on available information

### Platform

- [ ] **PLAT-01**: Skill works in Claude Desktop (ZIP upload)
- [ ] **PLAT-02**: Skill works in Claude Code (~/.claude/skills/)
- [ ] **PLAT-03**: Same SKILL.md works on both platforms without modification

### Distribution

- [ ] **DIST-01**: Skill packaged as ZIP file for team distribution
- [ ] **DIST-02**: Setup instructions provided for non-technical team members

## v2 Requirements

Deferred to future release. Tracked but not in current roadmap.

### Enhanced Intelligence

- **V2-01**: Call type detection (intro call vs QBR vs workspace review)
- **V2-02**: Automatic subject line generation
- **V2-03**: Sentiment analysis for tone adjustment

### Multi-Format Support

- **V2-04**: Support for non-Gong transcripts (Fireflies, Otter, manual notes)
- **V2-05**: HTML email output option for richer formatting

## Out of Scope

Explicitly excluded. Documented to prevent scope creep.

| Feature | Reason |
|---------|--------|
| Automatic email sending | Output is copy/paste only — user controls send |
| Gmail/Outlook integration | Manual paste into email client sufficient |
| Automatic Gong transcript fetching | User copy/pastes transcript — no API integration |
| CRM integration (Salesforce/HubSpot) | Outside skill scope, adds complexity |
| Meeting scheduling/calendar integration | Separate concern from email drafting |
| Code execution / Python scripts | Pure markdown skill, no dependencies |

## Traceability

Which phases cover which requirements. Updated during roadmap creation.

| Requirement | Phase | Status |
|-------------|-------|--------|
| EXTR-01 | TBD | Pending |
| EXTR-02 | TBD | Pending |
| EXTR-03 | TBD | Pending |
| EXTR-04 | TBD | Pending |
| GEN-01 | TBD | Pending |
| GEN-02 | TBD | Pending |
| GEN-03 | TBD | Pending |
| GEN-04 | TBD | Pending |
| GEN-05 | TBD | Pending |
| CONF-01 | TBD | Pending |
| CONF-02 | TBD | Pending |
| CONF-03 | TBD | Pending |
| DLVR-01 | TBD | Pending |
| DLVR-02 | TBD | Pending |
| PLAT-01 | TBD | Pending |
| PLAT-02 | TBD | Pending |
| PLAT-03 | TBD | Pending |
| DIST-01 | TBD | Pending |
| DIST-02 | TBD | Pending |

**Coverage:**
- v1 requirements: 19 total
- Mapped to phases: 0
- Unmapped: 19 (will be mapped during roadmap creation)

---
*Requirements defined: 2025-01-21*
*Last updated: 2025-01-21 after initial definition*
