# Phase 3: Distribution - Research

**Researched:** 2026-01-21
**Domain:** Claude Skills Packaging and Distribution
**Confidence:** HIGH

## Summary

Phase 3 packages the existing SKILL.md for team distribution across Claude Desktop (ZIP upload) and Claude Code (~/.claude/skills/). The research confirms that the same SKILL.md file works identically on both platforms - the difference is only in how it's delivered (ZIP for Desktop, folder for Code).

**Key Findings:**
- Claude Desktop and Claude Code both use the identical SKILL.md format - cross-platform compatibility is built into the Agent Skills standard
- ZIP packaging requires the skill folder as the root of the archive (not loose files) - this is the most common failure point
- Skills do NOT sync between platforms - each user must install separately on their preferred platform
- The distribution is straightforward: SKILL.md + README.md in a ZIP file, with 3-4 step instructions for each platform

**Primary recommendation:** Create `email-follow-up.zip` containing the `email-follow-up/` folder with SKILL.md and README.md. Provide separate installation instructions for Desktop (ZIP upload) and Code (extract to ~/.claude/skills/), keeping both under 5 steps each.

## Standard Stack

### Core Components

| Component | Format | Purpose | Why Standard |
|-----------|--------|---------|--------------|
| SKILL.md | Markdown + YAML frontmatter | Skill definition file | Official Agent Skills open standard format |
| ZIP archive | .zip | Desktop packaging | Required format for Claude Desktop/Web upload |
| Folder structure | Directory | Code installation | Direct folder copy to ~/.claude/skills/ |
| README.md | Markdown | Installation instructions | Universal documentation format |

### No External Dependencies Required

This phase requires **zero tooling beyond standard macOS utilities**:
- `zip` command for archive creation (built-in)
- Finder for manual ZIP creation (built-in)
- Text editor for README (any)

**Why this works:**
- The skill itself is pure markdown text - no code to compile or dependencies to install
- Both target platforms (Desktop and Code) support the identical SKILL.md format
- Team members only need to follow simple copy/upload instructions

### Alternatives Considered

| Instead of | Could Use | Tradeoff |
|------------|-----------|----------|
| ZIP file | GitHub repo link | Adds complexity - team would need git knowledge |
| README.md | Screenshot guide | Decisions specify text-only, no screenshots |
| Manual distribution | Shared Drive auto-sync | Over-engineering for small team distribution |

## Architecture Patterns

### Recommended ZIP Structure

```
email-follow-up.zip
└── email-follow-up/           # Skill folder (REQUIRED - not loose files)
    ├── SKILL.md               # Core skill file (REQUIRED)
    └── README.md              # Installation instructions
```

**Critical:** The ZIP must contain the folder, not just the files directly. Claude Desktop looks for SKILL.md inside a named folder.

**Incorrect structure (will fail silently):**
```
email-follow-up.zip            # WRONG
├── SKILL.md                   # Claude can't find it without folder wrapper
└── README.md
```

### Pattern 1: Cross-Platform Single Source

**What:** Maintain one SKILL.md that works on both Claude Desktop and Claude Code
**When to use:** Always - this is the Agent Skills standard design
**Why it works:** The Agent Skills open standard defines a portable format; both platforms implement the same spec

```
Distribution:
├── email-follow-up.zip        # For Claude Desktop users
│   └── email-follow-up/
│       └── SKILL.md           # Same file in both
└── email-follow-up/           # For Claude Code users (extract from ZIP or copy directly)
    └── SKILL.md               # Identical to Desktop version
```

**Source:** [Anthropic Skills GitHub](https://github.com/anthropics/skills) - "Claude Code skills follow the Agent Skills open standard, which works across multiple AI tools"

### Pattern 2: README as Dual-Platform Guide

**What:** Single README with clearly separated Desktop and Code instructions
**When to use:** When distributing to mixed-platform team
**Structure:**

```markdown
# Email Follow-Up Skill - Installation

## For Claude Desktop (Web)

1. Open Settings (profile icon, bottom left)
2. Go to Capabilities
3. Click "Upload skill" and select this ZIP file
4. Toggle the skill ON

## For Claude Code

1. Extract ZIP or copy the email-follow-up folder
2. Move to: ~/.claude/skills/email-follow-up/
3. Restart Claude Code or start new session
```

**Source:** Context decisions - "Text steps only, no screenshots"

### Anti-Patterns to Avoid

- **Zipping files without folder wrapper:** Claude Desktop silently fails when SKILL.md is at ZIP root
- **Platform-specific SKILL.md variations:** Creates maintenance burden; single file works everywhere
- **Verbose README with troubleshooting:** Decisions specify "keep minimal, no troubleshooting section"
- **Including example files:** Decisions specify "No example transcripts or additional files needed"

## Don't Hand-Roll

Problems that look simple but have existing solutions:

| Problem | Don't Build | Use Instead | Why |
|---------|-------------|-------------|-----|
| ZIP creation | Custom script | macOS `zip -r` or Finder | Built-in tools are sufficient |
| Installation automation | Install script | Manual 3-step instructions | Team is non-technical; scripts add complexity |
| Version management | Git-based workflow | Re-share ZIP when updated | Small team, infrequent updates |
| Platform detection | Auto-detect logic | Separate sections in README | Clear, explicit instructions are simpler |

**Key insight:** The distribution is simple text files in a ZIP. The complexity is in clear instructions for non-technical users, not in tooling.

## Common Pitfalls

### Pitfall 1: Incorrect ZIP Structure (Most Common)

**What goes wrong:** ZIP upload succeeds but skill never triggers
**Why it happens:** Users zip the files directly instead of the containing folder
**How to avoid:**
- Always `cd` to parent directory before zipping: `cd ~/Downloads && zip -r email-follow-up.zip email-follow-up/`
- Or right-click the FOLDER (not files) and "Compress" in Finder
**Warning signs:** Upload succeeds but skill doesn't appear or never activates

**Correct command:**
```bash
# Navigate to parent of skill folder
cd ~/Downloads/emailSkill

# Zip the folder itself
zip -r email-follow-up.zip email-follow-up/

# Verify structure
unzip -l email-follow-up.zip | head -5
# Should show: email-follow-up/SKILL.md (not just SKILL.md)
```

**Source:** [How to Actually Upload Claude Skills (Medium)](https://medium.com/@creativeaininja/how-to-actually-upload-claude-skills-without-breaking-everything-1e8c436df2f2) - "You need to zip THE FOLDER, not the contents of the folder"

### Pitfall 2: Forgetting to Toggle Skill ON

**What goes wrong:** Skill uploaded but doesn't activate
**Why it happens:** Claude Desktop requires explicit enable after upload
**How to avoid:** Include "Toggle the skill ON" as explicit step in README
**Warning signs:** Skill appears in list but Claude doesn't use it

**Source:** [Claude Help Center](https://support.claude.com/en/articles/12512180-using-skills-in-claude) - "Toggle any skill on or off using the switch next to it"

### Pitfall 3: Claude Code Wrong Directory

**What goes wrong:** Skill not discovered by Claude Code
**Why it happens:** User places SKILL.md directly in ~/.claude/skills/ instead of in a named subfolder
**How to avoid:** Explicit path with folder name: `~/.claude/skills/email-follow-up/SKILL.md`
**Warning signs:** `/skills` command doesn't show the skill

**Correct structure:**
```
~/.claude/skills/
└── email-follow-up/           # Named folder required
    └── SKILL.md               # Skill file inside folder
```

**Incorrect:**
```
~/.claude/skills/
└── SKILL.md                   # Wrong - no containing folder
```

**Source:** [Claude Code Docs](https://code.claude.com/docs/en/skills) - "Skills are stored at ~/.claude/skills/<skill-name>/SKILL.md"

### Pitfall 4: Skills Don't Sync Between Platforms

**What goes wrong:** User expects Desktop skill to appear in Code (or vice versa)
**Why it happens:** Platforms are independent; no automatic sync
**How to avoid:** State clearly in README: "Install separately on each platform you use"
**Warning signs:** User confusion about why skill works in one place but not another

**Source:** [The Definitive Guide to Claude SKILLS (Substack)](https://limitededitionjonathan.substack.com/p/the-definitive-guide-to-claude-skills) - "Skills do not sync between platforms. Each system is independent"

### Pitfall 5: Case Sensitivity in Skill Name

**What goes wrong:** Skill folder name doesn't match expectations
**Why it happens:** macOS is case-insensitive but Linux (some Code users) is case-sensitive
**How to avoid:** Use lowercase with hyphens: `email-follow-up` (matches name field in SKILL.md)
**Warning signs:** Works on macOS but not on Linux

## Code Examples

### Example 1: ZIP Creation Command (macOS)

```bash
# From the project root (where email-follow-up folder is)
cd /Users/david.cogan@postman.com/Downloads/emailSkill

# Create the distribution folder if needed
mkdir -p email-follow-up
cp SKILL.md email-follow-up/
# README.md is created/added here too

# Create ZIP with correct structure
zip -r email-follow-up.zip email-follow-up/

# Verify the structure (should show folder/SKILL.md not just SKILL.md)
unzip -l email-follow-up.zip
```

**Expected output:**
```
Archive:  email-follow-up.zip
  Length      Date    Time    Name
---------  ---------- -----   ----
        0  2026-01-21 12:00   email-follow-up/
    15234  2026-01-21 12:00   email-follow-up/SKILL.md
      456  2026-01-21 12:00   email-follow-up/README.md
```

### Example 2: README.md Template

```markdown
# Email Follow-Up Skill

Generate professional follow-up emails from Gong call transcripts.

## Installation

### Claude Desktop / Claude Web

1. Click your profile icon (bottom left corner)
2. Go to **Settings** > **Capabilities**
3. Click **Upload skill** and select this ZIP file
4. Find "gong-follow-up-email" in the Skills list and toggle it **ON**

### Claude Code

1. Extract the ZIP file
2. Copy the `email-follow-up` folder to `~/.claude/skills/`
   ```
   cp -r email-follow-up ~/.claude/skills/
   ```
3. Start a new Claude Code session

## Changing Your Signature

The default signature is "Best,\n[YOUR NAME]". To change it:

1. Open SKILL.md in a text editor
2. Find the "Signature" section near the bottom
3. Replace "[YOUR NAME]" with your name
4. Save and re-upload (Desktop) or restart session (Code)

Or just edit the signature when you paste the email into Gmail.

## Usage

Paste a Gong transcript and say "generate a follow-up email from this transcript"
```

### Example 3: Signature Customization Location in SKILL.md

```markdown
### Signature

- Default: "Best,\n[YOUR NAME]"
- User can configure: "use signature: Cheers,\nSarah"
- Apply configured signature for the session
```

**User action:** Find `[YOUR NAME]` in SKILL.md, replace with actual name (e.g., "David")

## State of the Art

| Aspect | Current Approach (2026) | Previous Approach | Impact |
|--------|-------------------------|-------------------|--------|
| Skill format | Agent Skills open standard (SKILL.md) | Platform-specific plugin systems | Cross-platform portability |
| Distribution | ZIP file with folder structure | Complex plugin installers | Simple team sharing |
| Installation | Settings > Capabilities > Upload | Developer-only command line | Non-technical user accessible |
| Platform coverage | Desktop, Web, Code, API all use same format | Different formats per platform | Write once, deploy everywhere |

**Recent changes (as of late 2025):**
- Agent Skills became an open standard, not just Anthropic-specific
- Claude Code merged slash commands with skills (same underlying system)
- Skills marketplace added for discovering pre-built skills
- Enterprise/Team can provision skills organization-wide

**Deprecated/outdated:**
- Old `.claude/commands/` structure is now merged into skills
- Separate upload flows for different platforms (now unified Settings > Capabilities)

## Open Questions

### Question 1: Default Signature Placeholder

**What we know:** Context decisions specify `[YOUR NAME]` as default placeholder
**What's unclear:** Whether to use literal `[YOUR NAME]` or leave as "David" (current SKILL.md default)
**Recommendation:** Use `[YOUR NAME]` as literal placeholder text - makes it obvious to team members they need to customize. The current SKILL.md says "Best,\nDavid" which would need updating.

### Question 2: ZIP File Naming

**What we know:** Context says "Claude's discretion" on ZIP file name
**What's unclear:** Best practice for naming
**Recommendation:** Use `email-follow-up.zip` - matches the skill folder name, lowercase with hyphens per Claude naming conventions (max 64 characters, no spaces)

### Question 3: Containing Folder vs Flat Extraction

**What we know:** ZIP must contain folder; Context says "Claude's discretion"
**What's unclear:** Whether README should be inside or outside the skill folder
**Recommendation:** README.md INSIDE the skill folder - this way the entire ZIP extracts to one self-contained folder that can be directly moved to ~/.claude/skills/

## Sources

### Primary (HIGH confidence)

- [Claude Help Center - Using Skills](https://support.claude.com/en/articles/12512180-using-skills-in-claude) - Official Anthropic documentation on skill installation and management
- [Claude Help Center - Creating Custom Skills](https://support.claude.com/en/articles/12512198-how-to-create-custom-skills) - ZIP structure requirements, required files
- [Claude Code Docs - Extend Claude with skills](https://code.claude.com/docs/en/skills) - ~/.claude/skills/ directory structure, SKILL.md format
- [Anthropic Skills GitHub Repository](https://github.com/anthropics/skills) - Official skill examples and structure patterns

### Secondary (MEDIUM confidence)

- [How to Actually Upload Claude Skills (Medium)](https://medium.com/@creativeaininja/how-to-actually-upload-claude-skills-without-breaking-everything-1e8c436df2f2) - ZIP structure pitfalls, folder-not-files requirement
- [The Definitive Guide to Claude SKILLS (Substack)](https://limitededitionjonathan.substack.com/p/the-definitive-guide-to-claude-skills) - Platform comparison, sync behavior, installation differences
- [Claude Skills Explained (Hostinger)](https://www.hostinger.com/tutorials/claude-skills) - Settings navigation, toggle behavior

### Tertiary (LOW confidence)

- Various README best practices articles - general guidance on installation instructions for non-technical users
- macOS zip command documentation - correct archive structure creation

## Metadata

**Confidence breakdown:**
- Standard stack: **HIGH** - Official Anthropic documentation confirms format
- Architecture patterns: **HIGH** - Agent Skills standard is well-documented
- ZIP structure: **HIGH** - Multiple sources confirm folder-wrapper requirement
- Pitfalls: **HIGH** - Well-documented common failures
- Cross-platform: **HIGH** - Same SKILL.md format verified across platforms

**Research date:** 2026-01-21
**Valid until:** ~90 days (skill format is stable; unlikely to change soon)
**Recommended re-research trigger:** Major Claude release announcing skills format changes
