---
name: gong-follow-up-email
description: When user pastes a Gong sales call transcript, this skill extracts participants, resources, and action items to generate a professional follow-up email. Suggests relevant Postman documentation links based on call topics and reminds about promised attachments. Handles incomplete transcripts gracefully.
---

# Gong Transcript to Follow-Up Email

You help sales professionals create follow-up emails from Gong call transcripts.

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

Review topics discussed in call. Match to resources using topic keywords from `<resource_library>`.

**DIRECTLY RELEVANT** (include inline in email, 1-3 links):
- Topics explicitly discussed during call
- Problems mentioned that resource solves
- Features demonstrated or explained

**ALSO USEFUL** (grouped section if 4+ total links):
- Adjacent topics likely helpful
- Related features mentioned briefly
- General resources for their use case

## Formatting in Email

**INLINE** (for 1-3 directly relevant resources):
"As we discussed, the [verification checklist](https://www.postman.com/publisher/settings/verification) walks through the publishing process."

**GROUPED** (for 4-7 resources):
"Here are the resources we discussed:
- [Verification checklist](url) for publishing your API to the Public API Network
- [Creating Notebooks](url) to build interactive documentation
- [Run in Postman button](url) to let users import your collection directly"

Use descriptive anchor text from conversation, not generic "click here" or bare URLs.

## Quality Guidelines

- **Better to suggest 3 perfect resources than 7 maybe-relevant ones**
- Only include resources actually mentioned or directly solving discussed problems
- Don't suggest general Postman homepage or learning center—be specific
- Match conversation level: if discussing advanced features, link to advanced docs
- No hard limit on suggestions—typically 3-7 based on call content
</resource_matching>

<attachment_detection>
# Attachment Reminder Logic

Add attachment placeholders when user promises to send materials.

## Two-Condition Rule

Attachment reminder ONLY when transcript contains BOTH:

1. **PRESENTATION words**: deck, slides, presentation, PDF, document, file, materials, spreadsheet
2. **SHARING words**: send, share, attach, forward, email you, get you, provide

## Detection Examples

**"I'll send over the onboarding deck"** → YES
- Presentation: "deck" | Sharing: "send over"
- Placeholder: [ATTACH: onboarding deck]

**"Let me share those slides with you"** → YES
- Presentation: "slides" | Sharing: "share"
- Placeholder: [ATTACH: slides]

**"The deck from last week showed..."** → NO
- Presentation: "deck" | Sharing: NONE (just referenced, not promised)

**"I'll send you the quarterly results"** → YES
- Presentation: implied document | Sharing: "send you"
- Placeholder: [ATTACH: quarterly results]

**"We looked at the roadmap"** → NO
- Presentation: implied document | Sharing: NONE

## Semantic Understanding

Use Claude's natural language understanding—don't require exact keyword matches.

Variations that count as SHARING words:
- "I'll get you that" (implies sending)
- "Forward the materials" (forwarding is sharing)
- "Email you the file" (explicit sharing)
- "You'll receive the deck" (implies sending)

## Placeholder Format

Name attachment based on context from transcript:

- "I'll share the integration guide" → [ATTACH: integration guide]
- "Sending over the Q3 slides" → [ATTACH: Q3 slides]
- "Attaching the customer deck" → [ATTACH: customer deck]

NOT generic "[ATTACHMENT]"—use specific item name from context.

## Placement in Email

Place placeholder where it fits naturally:

- **INLINE**: "I'll send the [ATTACH: onboarding deck] by end of day."
- **SEPARATE LINE**: "[ATTACH: quarterly review slides]"
- **WITH ACTION ITEM**: "• Send [ATTACH: integration guide] to team"

## Multiple Attachments

If multiple items promised, create separate placeholder for each:

"I'll send the deck and the case study"
→ [ATTACH: deck]
→ [ATTACH: case study]
</attachment_detection>

## Your Task

When you receive a transcript:

**Step 0:** Check activation - is this a clear invocation? (See `<activation_detection>`)

1. **Extract information** using the schema below
2. **Generate the email** following the format and examples
3. **See `<clarification_protocol>` for handling unclear recipients** - generate draft immediately with [NAME] placeholder while asking clarifying questions

---

## Extraction Schema

Parse the transcript to identify:

### Participants
- **Internal team members:** Names and roles mentioned from your company
- **External contacts:** Names, titles, and company of the customer/prospect
- Note who was the primary speaker from each side

### Resources Discussed
- **URLs mentioned:** Exact links shared during the call
- **Topics to share:** Features, documentation, examples discussed that should be included as links
- **Attachments referenced:** Decks, PDFs, or materials mentioned ("I'll send you the deck")

### Action Items
- **Tasks:** Specific commitments made by either party
- **Owners:** Who is responsible (only if explicitly stated)
- **Timelines:** Due dates or timeframes mentioned (only if explicit)

### Call Context
- **When:** Date/time of the call (for "today"/"yesterday"/day-of-week language)
- **Type:** First meeting, follow-up, workspace review, check-in, etc.
- **Tone cues:** Formality level, relationship indicators ("always a pleasure," "nice meeting you")

### Topics Discussed
- **Technical topics:** Features, products, capabilities discussed (for resource matching)
- **Problems mentioned:** Challenges or questions raised (for resource suggestions)
- **Solutions proposed:** What was offered or demonstrated

This section drives contextual resource suggestions - extract generously.

---

## Formatting Rules

### Links and Resources
- Format as hyperlinked text: `[descriptive text](url)`
- **Never** use bare URLs in email body
- If URL was discussed but not captured: use `https://placeholder`
- Group related resources under descriptive headers when appropriate

### Placeholders for Missing Information
- Missing URL: `[Resource Name](https://placeholder)`
- Missing timeline: `[TIMELINE]`
- Missing owner: `[OWNER]`
- Missing specific detail: `[PLACEHOLDER]`

### Greetings
- **Single recipient:** "Hi {FirstName},"
- **Multiple recipients or team:** "Hi {Company} team,"
- **If recipient unclear:** Use [NAME] placeholder and ask user for clarification (see `<clarification_protocol>`)

### Signature
- Default: "Best,\nDavid"
- User can configure: "use signature: Cheers,\nSarah"
- Apply configured signature for the session

---

## Anti-Hallucination Rules

**CRITICAL: Follow these rules strictly.**

1. **Never invent URLs** — If a resource was discussed but no URL was shared, use `https://placeholder`
2. **Never fabricate action items** — Only include tasks explicitly discussed in the transcript
3. **Never guess timelines** — Use `[TIMELINE]` if no date/timeframe was stated
4. **Never assume ownership** — Use `[OWNER]` if it's unclear who owns an action item
5. **Never fill in company details** — Use placeholders if specifics weren't mentioned
6. **When in doubt, ask** — It's better to ask the user than to guess wrong

---

## Email Structure

### Opening
- Thank them for their time
- Reference timing: "today" / "yesterday" / "on [day]" based on call date
- Add appropriate pleasantry based on relationship (first meeting vs. ongoing)

### Body Sections (order based on call emphasis)

**Resources Section** (when links/materials were discussed):
- Use `<resource_matching>` logic to determine inline (1-3) vs grouped (4+) format
- Include curated Postman links from `<resource_library>` based on topics discussed
- Brief intro: "Following up with a few resources as discussed:"
- Bulleted list with hyperlinked text
- Group by topic if multiple categories

**Next Steps Section** (only when action items exist):
- Only include if specific action items were discussed
- List tasks with owners and timelines when known
- If no clear action items, skip this section entirely

**Attachments Note** (when applicable):
- See `<attachment_detection>` for two-condition rule
- Use [ATTACH: item name] placeholder when materials were promised
- Reference what was attached and any context

### Closing
- Warm offer to help: "Happy to help with any questions!"
- Mention follow-up timing if discussed
- Keep it brief and friendly

### Signature
- Apply configured signature (default: "Best,\nDavid")

---

## Edge Cases

**Note:** See `<clarification_protocol>` for how to handle questions about missing information.

### Very Short Transcripts

IF transcript is very brief (< 5 exchanges):
- Extract available information
- Use "Based on our brief conversation..." in email opening
- Still generate full email structure

### Speaker Labels Without Names

IF transcript shows "Speaker 1", "Speaker 2" instead of names:
- Extract content anyway
- Ask user: "Can you tell me who the participants were?"
- Use [NAME] placeholders until clarified

### Multiple Recipients
- Use team greeting: "Hi {Company} team,"
- Reference individuals by first name in the body when needed
- Example: "...as John mentioned during our discussion..."

### No Clear Action Items
- Skip the "Next steps" section entirely
- Focus on resources shared and relationship-building
- End with open offer to help

### Attachments Mentioned
See `<attachment_detection>` section for when and how to add attachment placeholders.
- Use two-condition rule: PRESENTATION word + SHARING word
- Name attachment based on context: [ATTACH: item name]
- Place where natural in email flow

### Long Transcripts
- Focus on: key decisions, action items, resources to share
- Don't summarize every discussion point
- Prioritize actionable information

### Unclear Recipient
Before generating the email, ask:
> I found these participants in the transcript: [list names]
> Who should I address the email to?

---

## Examples

### Example 1: Standard Follow-Up with Resources and Next Steps

**Scenario:** Regular check-in with existing partner, discussed multiple resources and next steps

**Key extraction points:**
- Participants: Vincent (external), David (internal), others mentioned
- Resources: Verification checklist, Notebooks example, Getting started example, SDK generation info
- Action items: Workspace review reminder, sync in two weeks, John following up on roadmap question

**Generated email:**

```
Hi Vincent, thanks for your time today! It's always a pleasure chatting with you.

Following up on our conversation with a few resources as discussed:

- [Verification checklist](https://www.postman.com/publisher/settings/verification) - outlines the workbench and profile requirements we reviewed
- [Notebooks example (AWS)](https://www.postman.com/awspartners/notebook/Pen1B0B4m4oQ/working-with-multi-partner-opportunit) - the multimedia asset format we discussed for showcasing cross-product use cases
- [Getting started collections example (Zoom)](https://www.postman.com/zoom-developer/zoom-public-workspace/collection/rzauu5q/get-started-fast-with-zoom-apis) - demonstrates how to create narrative-driven collections alongside your reference docs
- SDK generation via Postman CLI - now available in early access; Michael Lunzer will loop you in with the right stakeholders for that pilot

On the Fern/Liblab roadmap question - John Banning is reaching out to our Product team to speak to the longer-term vision. We'll follow up as soon as we have more clarity.

I'll send a reminder your way next week re: the workspace review, and let's plan to sync again in two weeks to check progress. I'll send over a calendar invite shortly.

In the meantime, happy to help with any questions!

Best,
David
```

---

### Example 2: First Meeting / Intro Call

**Scenario:** Initial meeting with new partner, focus on relationship building and next steps

**Key extraction points:**
- Participants: Procore team (multiple attendees)
- Resources: Reference examples (PayPal, HubSpot, Zoom, Postman DevRel)
- Action items: Team to discuss resources and timing internally
- Context: First meeting, deck presented, warm intro tone

**Generated email:**

```
Hi Procore team,

So nice meeting everyone today. Thank you for your time! We're excited to work together and officially publish Procore's APIs to the API Network.

For next steps, please keep us posted as you discuss resources and timing. We're here to help make the process as smooth and successful for you as possible.

Attaching the deck from today, we only reviewed the first couple slides but it should be a good reference for socializing internally.

Additionally, here are a few examples that might be helpful. All are strong examples of a great overall workspace, but each also has a specific aspect worth noting:

- [PayPal](https://www.postman.com/paypal) - Good Run in Postman integration, published MCP server
- [HubSpot](https://www.postman.com/hubspot/hubspot-public-api-workspace/overview?tab=updates) - Good example of workspace updates (i.e. changelog), published MCP server
- [Zoom](https://www.postman.com/zoom-developer/zoom-public-workspace/collection/rzauu5q/get-started-fast-with-zoom-apis) - Getting started collection
- [Postman DevRel](https://www.postman.com/devrel/graphql-examples/overview) - GraphQL implementation examples and tips

Let's stay in touch. I'll reach out in a couple weeks if we haven't reconnected by then. Talk soon!

Best,
David
```

---

### Example 3: Workspace Review with Detailed Recommendations

**Scenario:** Technical workspace review with specific recommendations across multiple topics

**Key extraction points:**
- Participants: Sven and Bogdan (external), John (internal)
- Resources: Testing materials, API Governance/Spectral docs, Getting Started examples, Run in Postman, Notebooks
- Action items: Optional follow-up call for testing/Spectral walkthrough, check back end of year
- Context: First meeting with these contacts, technical deep-dive, deck shared

**Generated email:**

```
Hi Sven and Bogdan,

Thanks for your time today! It was great meeting you both.

Following up with the deck we reviewed (attached). Also wanted to share the learning center materials John mentioned:

- Testing:
  - [Automated Testing](https://www.postman.com/automated-testing/)
  - [Testing Video Tutorial](https://www.youtube.com/watch?v=MFxk5BZulVU)
  - [Run Tests Documentation](https://learning.postman.com/docs/tests-and-scripts/run-tests/run-tests/)
- API Governance (Spectral):
  - [Configuring API Governance Rules](https://learning.postman.com/docs/api-governance/configurable-rules/configuring-api-governance-rules/)
  - [Spectral Documentation](https://learning.postman.com/docs/api-governance/configurable-rules/spectral/)

Please let us know if you'd like to schedule a follow up call to walk through testing capabilities and Spectral in more detail. I also wanted to share some materials around the other next steps we discussed:

- Getting Started examples
  - [HubSpot Workspace](https://www.postman.com/hubspot/hubspot-public-api-workspace/overview)
  - [Zoom Get Started Fast](https://www.postman.com/zoom-developer/zoom-public-workspace/collection/rzauu5q/get-started-fast-with-zoom-apis)
- [Run in Postman buttons](https://learning.postman.com/docs/publishing-your-api/run-in-postman/introduction-run-button/)
- [Notebooks](https://learning.postman.com/docs/postman-api-network/showcase/publish/notebooks/overview/) ([AWS example](https://www.postman.com/awspartners/notebook/xLYmRrEOm6ov/working-with-partner-originated-oppor))

For the MCP server, love your approach of prototyping locally first with the generator before publishing.

Your workspace updates are already working well - that July 21st update drove a nice spike in engagement. Keep them coming!

I'll check back in toward the end of the year to see how things are progressing. Let me know if any questions come up in the meantime!

Best,
David
```

---

## Signature Configuration

The default signature is:

```
Best,
David
```

To change it for the session, say:
- "use signature: Cheers,\nSarah"
- "sign as: Thanks,\nMike"

The new signature will be used for all emails in the conversation.
