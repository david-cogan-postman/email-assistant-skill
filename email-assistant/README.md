# Email Assistant Skill for Claude Desktop

Writes any email in David's voice. Supports structured Gong follow-ups, customer/partner correspondence, and cold outreach.

## Installation

1. Unzip `email-assistant-v1.0.zip`
2. Copy the `email-assistant/` folder to your Claude Desktop custom skills directory:
   - macOS: `~/Library/Application Support/Claude/skills/`
   - Windows: `%APPDATA%\Claude\skills\`
3. Restart Claude Desktop

## Usage

### Gong Follow-Up (auto-detected)
Paste a Gong transcript and ask for a follow-up email:
```
[paste transcript]
Generate a follow-up email from this call
```

### External Professional
Default mode for customer/partner emails:
```
Write an email to the Stripe team confirming our meeting next Tuesday
```

### Cold Outreach
Explicitly request outreach mode:
```
Write a cold outreach email to MongoDB about their API workspace
```

## Structure

```
email-assistant/
├── SKILL.md         # Main orchestrator + mode detection
├── PROFILE.md       # User context (role, team, approach)
├── VOICE.md         # Writing style patterns (8 samples analyzed)
├── MODES/
│   ├── GONG_FOLLOWUP.md   # Gong transcript → follow-up
│   ├── EXTERNAL.md        # Customer/partner correspondence
│   └── OUTREACH.md        # Cold/warm introductions
└── RESOURCES/
    ├── POSTMAN.md         # Postman documentation library
    └── TEMPLATE.md        # Template for adding other domains
```

## Voice Calibration

| Context | Formality | Opening | Closing |
|---------|-----------|---------|---------|
| External Professional | 6-7/10 | "Hi [Name]," + pleasantry | "Best," |
| Cold Outreach | 5-6/10 | "Hi [Name]," + role intro | "Best," |
| Internal | 4-5/10 | "Hi folks" / "Hi [Name]" | "Best," |

Key phrases: "Happy to help", "Would you have 30 minutes?", "Hope 2026 is treating you well!", "I'm reaching out because..."
