# Architecture Research: Claude Skill Structure

**Domain:** Email generation from call transcripts
**Researched:** 2026-01-21
**Confidence:** HIGH

## Executive Summary

Claude skills follow a progressive disclosure architecture where a core SKILL.md file orchestrates access to supporting resources. For markdown-only skills (no code execution), the architecture focuses on clear information hierarchy and selective context loading. The email skill should use a minimal three-component structure: main instructions (SKILL.md), email templates (template file), and reference resources (links library).

This architecture enables Claude to load only what's needed for each task, keeping context efficient while providing comprehensive guidance for email generation.

## Components

### Required Component

**SKILL.md** (Required)
- **Purpose:** Main entry point and orchestrator
- **Contents:**
  - YAML frontmatter (name, description)
  - High-level workflow instructions
  - References to supporting files
  - Context about when/how to use the skill
- **Size guideline:** Under 500 lines for optimal performance
- **Token cost:** Loaded when skill activates

### Supporting Components (Optional)

**Template Files**
- **Purpose:** Provide structure for Claude to fill in
- **For this skill:** Email format template showing structure, tone, required sections
- **Naming:** `EMAIL_TEMPLATE.md` or `template.md`
- **Token cost:** Loaded only when referenced from SKILL.md

**Reference Files**
- **Purpose:** Detailed information loaded on-demand
- **For this skill:** Postman resource library (links, documentation URLs, common references)
- **Naming:** `RESOURCES.md` or `reference.md`
- **Token cost:** Loaded only when Claude needs specific information

**Configuration Files** (optional)
- **Purpose:** User-specific customization (signature, preferences)
- **For this skill:** Sender signature, tone preferences
- **Naming:** `CONFIG.md` or embedded in frontmatter
- **Token cost:** Loaded with SKILL.md if small, separate file if larger

## Recommended File Structure

For the Gong transcript email skill:

```
postman-email-skill/
├── SKILL.md              # Main instructions and workflow
├── EMAIL_TEMPLATE.md     # Email format and structure
├── RESOURCES.md          # Postman links and references
└── CONFIG.md             # User configuration (signature, preferences)
```

### Alternative Minimal Structure

For simplicity, especially in early versions:

```
postman-email-skill/
└── SKILL.md              # All content in single file
```

Start simple, split later if SKILL.md approaches 500 lines.

## Information Flow

### Skill Activation Flow

```
User uploads transcript
         ↓
Claude recognizes task matches skill description
         ↓
Skill tool invokes with SKILL.md content
         ↓
SKILL.md content loaded into context
         ↓
Claude follows workflow instructions
         ↓
[Optional] Claude reads EMAIL_TEMPLATE.md for format
         ↓
[Optional] Claude reads RESOURCES.md for specific links
         ↓
Claude generates email following templates
         ↓
Output returned to user
```

### Progressive Disclosure in Action

**Scenario 1: Simple follow-up**
- User uploads transcript
- Claude reads SKILL.md (workflow + basic instructions)
- Claude generates email using instructions in SKILL.md
- **Files accessed:** SKILL.md only
- **Token cost:** Minimal

**Scenario 2: Email with specific Postman resources**
- User uploads transcript mentioning integrations
- Claude reads SKILL.md (sees reference to RESOURCES.md)
- Claude reads RESOURCES.md to find integration documentation links
- Claude generates email with appropriate links
- **Files accessed:** SKILL.md + RESOURCES.md
- **Token cost:** Moderate

**Scenario 3: Custom formatting request**
- User asks for specific email structure
- Claude reads SKILL.md (sees reference to EMAIL_TEMPLATE.md)
- Claude reads EMAIL_TEMPLATE.md for detailed format guidance
- Claude generates email following template
- **Files accessed:** SKILL.md + EMAIL_TEMPLATE.md
- **Token cost:** Moderate

## Component Boundaries

### SKILL.md Responsibilities

**OWNS:**
- Workflow steps (how to process transcript → email)
- High-level instructions (parse transcript, identify action items, generate follow-up)
- File references (when to read template, when to consult resources)
- Skill metadata (name, description for discovery)
- Basic email guidelines (tone: professional but friendly, length: concise)

**DOES NOT OWN:**
- Detailed email structure (→ EMAIL_TEMPLATE.md)
- Comprehensive link library (→ RESOURCES.md)
- Extensive examples (→ EXAMPLES.md if needed)

### EMAIL_TEMPLATE.md Responsibilities

**OWNS:**
- Email structure (greeting, body sections, closing)
- Formatting conventions (how to format action items, questions, etc.)
- Tone examples (sample phrases, voice)
- Section requirements (what must be included)

**DOES NOT OWN:**
- Workflow logic (→ SKILL.md)
- Postman-specific links (→ RESOURCES.md)
- Processing instructions (→ SKILL.md)

### RESOURCES.md Responsibilities

**OWNS:**
- Postman documentation links
- Common resource categories (Getting Started, Integrations, API docs)
- Link descriptions (when to use each resource)
- Resource discovery (how to find appropriate links)

**DOES NOT OWN:**
- Email formatting (→ EMAIL_TEMPLATE.md)
- Workflow steps (→ SKILL.md)

### CONFIG.md Responsibilities (Optional)

**OWNS:**
- User signature
- Sender name/title
- Tone preferences (if customizable)
- Company-specific context

**DOES NOT OWN:**
- Email structure (→ EMAIL_TEMPLATE.md)
- Processing logic (→ SKILL.md)

## Build Order Recommendations

### Phase 1: Single-File MVP

**Goal:** Prove the concept works end-to-end

**Action:** Create SKILL.md with everything inline:
```markdown
---
name: postman-email-skill
description: Generate professional follow-up emails from Gong call transcripts for Publisher Success managers at Postman
---

# Postman Email Skill

## Workflow
[Step-by-step instructions]

## Email Template
[Inline template structure]

## Common Resources
[Inline list of key Postman links]

## Configuration
[Inline signature/preferences]
```

**Why first:**
- Fastest path to working skill
- Tests core workflow immediately
- Easier to iterate on single file
- Can identify what needs separation

**Validation:** Can Claude generate a good email from a transcript?

### Phase 2: Extract Template

**Goal:** Separate email formatting from workflow logic

**Trigger:** When template section in SKILL.md becomes detailed (>100 lines) or when you want to test multiple template variations

**Action:**
1. Create EMAIL_TEMPLATE.md
2. Move detailed formatting instructions from SKILL.md
3. Update SKILL.md to reference: "For email format, see [EMAIL_TEMPLATE.md](EMAIL_TEMPLATE.md)"

**Why second:**
- Template likely to be most detailed component
- Clear separation of concerns (workflow vs format)
- Enables template iteration without touching workflow

**Validation:** Does Claude correctly reference and apply the external template?

### Phase 3: Extract Resources

**Goal:** Separate content library from instructions

**Trigger:** When resources list grows large (>50 links) or resources categorized by topic

**Action:**
1. Create RESOURCES.md
2. Move Postman links library from SKILL.md
3. Organize by category (Getting Started, Integrations, API, etc.)
4. Update SKILL.md: "For Postman documentation links, see [RESOURCES.md](RESOURCES.md)"

**Why third:**
- Resource library likely to grow over time
- Not needed for every email (selective loading valuable)
- Clean separation enables resource updates without touching core skill

**Validation:** Does Claude find and include relevant links when appropriate?

### Phase 4: Add Configuration (Optional)

**Goal:** Enable user customization

**Trigger:** When multiple users share skill or user preferences become complex

**Action:**
1. Create CONFIG.md
2. Move signature, preferences from SKILL.md
3. Document configuration options
4. Update SKILL.md to read CONFIG.md

**Why last:**
- Simplest to embed initially
- Premature abstraction if only one user
- Easy to add later when needed

**Validation:** Can users easily customize signature/preferences?

## Architecture Patterns

### Pattern 1: Progressive Disclosure (Core Pattern)

**Principle:** Only load what's needed for the current task

**Implementation:**
- Keep SKILL.md focused on workflow and navigation
- Reference detailed content in separate files
- Use clear file names that indicate content

**Example:**
```markdown
# SKILL.md
For email format guidance, see [EMAIL_TEMPLATE.md](EMAIL_TEMPLATE.md)
For Postman resources, see [RESOURCES.md](RESOURCES.md)
```

**Why it works:**
- Reduces token usage
- Keeps context focused
- Scales to larger skills

### Pattern 2: Template Pattern

**Principle:** Provide structure for Claude to fill in

**Implementation:**
- Create template file showing desired output structure
- Include placeholders or examples
- Reference from SKILL.md when format consistency matters

**Example:**
```markdown
# EMAIL_TEMPLATE.md

Subject: Following up on our [meeting topic] conversation

Hi [Name],

Thanks for taking the time to [specific discussion point from transcript].

[Action items or next steps]

[Relevant resources]

Best regards,
[Signature]
```

**Why it works:**
- Ensures consistent output format
- Reduces ambiguity about structure
- Makes quality requirements explicit

### Pattern 3: Reference Library Pattern

**Principle:** Centralize frequently-used information for on-demand access

**Implementation:**
- Create reference file organized by category
- Use clear section headers for discovery
- Reference from SKILL.md with guidance on when to consult

**Example:**
```markdown
# RESOURCES.md

## Getting Started
- [Postman Learning Center](https://learning.postman.com)
- [Quick Start Guide](https://learning.postman.com/docs/getting-started)

## Integrations
- [API Integrations Overview](https://www.postman.com/api-platform/api-integrations)
...
```

**Why it works:**
- Scales to large reference libraries
- Enables selective loading
- Easy to maintain and update

### Pattern 4: Workflow-Driven Instructions

**Principle:** Structure instructions as clear sequential steps

**Implementation:**
- Number steps explicitly
- Include validation/checking within workflow
- Make decision points clear

**Example:**
```markdown
## Workflow

1. **Parse transcript**
   - Identify participants
   - Extract key discussion points
   - Note action items and questions

2. **Determine email purpose**
   - Follow-up on action items?
   - Answer questions?
   - Share resources?

3. **Generate email**
   - Apply format from EMAIL_TEMPLATE.md
   - Include relevant links from RESOURCES.md if applicable
   - Use signature from CONFIG.md
```

**Why it works:**
- Reduces ambiguity
- Matches how Claude processes tasks
- Easy to debug and improve

## Anti-Patterns to Avoid

### Anti-Pattern 1: Deeply Nested References

**What it looks like:**
```
SKILL.md → references advanced.md → references details.md → actual content
```

**Why it's bad:**
- Claude may partially read nested files (using head -100)
- Results in incomplete information
- Increases token usage with multiple file reads

**Instead:** Keep all references one level deep from SKILL.md

### Anti-Pattern 2: Duplicated Information

**What it looks like:**
- Same email format described in SKILL.md and EMAIL_TEMPLATE.md
- Same links listed in SKILL.md and RESOURCES.md

**Why it's bad:**
- Wastes tokens loading duplicate content
- Creates maintenance burden (update in two places)
- Can cause confusion if versions diverge

**Instead:** Information lives in exactly one place, SKILL.md references it

### Anti-Pattern 3: Premature File Splitting

**What it looks like:**
- Creating EMAIL_TEMPLATE.md with 10 lines
- Creating RESOURCES.md with 3 links
- Creating CONFIG.md for single signature line

**Why it's bad:**
- Adds file navigation overhead for minimal benefit
- More complex for simple use cases
- Progressive disclosure only helps when files are substantial

**Instead:** Start with single SKILL.md, split when components grow large

### Anti-Pattern 4: Vague File References

**What it looks like:**
```markdown
See the other file for format details.
Check the reference document for links.
```

**Why it's bad:**
- Claude doesn't know which file to read
- Increases uncertainty and potential errors
- Wastes tokens searching for right file

**Instead:** Use explicit references with full file names:
```markdown
For email format, see [EMAIL_TEMPLATE.md](EMAIL_TEMPLATE.md)
For Postman resources, see [RESOURCES.md](RESOURCES.md)
```

### Anti-Pattern 5: Assuming All Files Load

**What it looks like:**
- SKILL.md that assumes Claude has read all supporting files
- Instructions that reference content without explicit file references

**Why it's bad:**
- Claude only reads files when instructed
- Missing context leads to incomplete outputs
- Defeats progressive disclosure benefits

**Instead:** Explicitly instruct Claude when to read supporting files:
```markdown
If the email needs Postman documentation links, read [RESOURCES.md](RESOURCES.md)
```

## Scalability Considerations

### From Single User to Team

**Single User:**
- Inline configuration acceptable
- Simple signature in SKILL.md
- Personal preferences embedded

**Team:**
- Extract CONFIG.md for per-user customization
- Shared SKILL.md and EMAIL_TEMPLATE.md
- Individual CONFIG.md files or variables

### From Simple to Complex

**Simple Skill (<200 lines):**
- Single SKILL.md with everything inline
- Fast iteration, minimal overhead

**Complex Skill (>500 lines):**
- Split into multiple files
- Progressive disclosure becomes valuable
- Easier maintenance and updates

### From Prototype to Production

**Prototype:**
- Everything in SKILL.md
- Quick validation of concept
- Easy to iterate

**Production:**
- Clean component separation
- Well-organized reference materials
- Clear documentation of file structure

## Distribution Considerations

### Claude Desktop (ZIP Upload)

**Structure:**
```
postman-email-skill.zip
└── postman-email-skill/
    ├── SKILL.md
    ├── EMAIL_TEMPLATE.md
    ├── RESOURCES.md
    └── CONFIG.md
```

**Considerations:**
- Users upload entire ZIP
- All files included in upload
- Easy to share complete package
- Users can customize CONFIG.md before zipping

### Claude Code (~/.claude/skills/)

**Structure:**
```
~/.claude/skills/postman-email-skill/
├── SKILL.md
├── EMAIL_TEMPLATE.md
├── RESOURCES.md
└── CONFIG.md
```

**Considerations:**
- Persistent across sessions
- Easy to update individual files
- Can customize without re-uploading
- Project-specific version possible at `.claude/skills/`

**Both environments support:**
- Same file structure
- Same progressive disclosure
- Same SKILL.md format
- No code changes needed

## Technical Constraints

### No Code Execution Required

**What this means:**
- Pure markdown instructions
- No scripts directory needed
- No Python/Bash utilities
- Simpler distribution

**Architectural implications:**
- All processing logic described in natural language
- Claude performs all analysis and generation
- Template pattern especially valuable (shows desired output)
- Clear examples critical for quality

### File Access Mechanism

**How Claude reads files:**
1. Skill activated based on description match
2. SKILL.md content loaded into context
3. Claude uses Read tool to access referenced files
4. Only explicitly referenced files are loaded

**Architectural implications:**
- File references must be explicit markdown links
- File names should be descriptive
- One level deep for reliability
- Clear navigation in SKILL.md

## Validation Strategy

### Component Validation

**SKILL.md:**
- [ ] Under 500 lines
- [ ] Clear workflow steps
- [ ] Explicit file references
- [ ] Description triggers on "Gong transcript" and "email"

**EMAIL_TEMPLATE.md:**
- [ ] Shows complete email structure
- [ ] Includes tone/voice guidance
- [ ] Clear section labels
- [ ] Example placeholders

**RESOURCES.md:**
- [ ] Organized by category
- [ ] Descriptive link labels
- [ ] Current URLs
- [ ] When-to-use guidance

### Integration Validation

**File references work:**
- [ ] Claude successfully reads EMAIL_TEMPLATE.md when needed
- [ ] Claude successfully reads RESOURCES.md when needed
- [ ] Claude doesn't load files unnecessarily

**Workflow works:**
- [ ] Transcript parsing accurate
- [ ] Email generation follows template
- [ ] Appropriate resources included
- [ ] Output quality consistent

## Sources

This research is based on official Claude Skills documentation and best practices from 2026:

**Primary Sources (HIGH confidence):**
- [Extend Claude with skills - Claude Code Docs](https://code.claude.com/docs/en/skills)
- [Skill authoring best practices - Claude Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/best-practices)
- [Agent Skills Overview - Claude Docs](https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview)

**Supporting Sources (MEDIUM confidence):**
- [Inside Claude Code Skills: Structure, prompts, invocation](https://mikhail.io/2025/10/claude-code-skills/)
- [Claude Skills and CLAUDE.md: a practical 2026 guide for teams](https://www.gend.co/blog/claude-skills-claude-md-guide)
- [GitHub - anthropics/skills: Public repository for Agent Skills](https://github.com/anthropics/skills)
- [Getting Claude Code to do my emails - Harper Reed's Blog](https://harper.blog/2025/12/03/claude-code-email-productivity-mcp-agents/)

**Verification:** All architectural patterns verified against official Anthropic documentation. Progressive disclosure pattern confirmed as core design principle in official best practices.
