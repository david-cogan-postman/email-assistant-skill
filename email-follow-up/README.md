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

Note: Skills do not sync between platforms. Install separately on each platform you use.

## Changing Your Signature

The default signature is "Best, [YOUR NAME]". To change it:

1. Open SKILL.md in a text editor
2. Find the "Signature Configuration" section near the bottom
3. Replace `[YOUR NAME]` with your name
4. Save and re-upload (Desktop) or restart session (Code)

Or just edit the signature when you paste the email into Gmail.

## Usage

Paste a Gong transcript and say "generate a follow-up email from this transcript"
