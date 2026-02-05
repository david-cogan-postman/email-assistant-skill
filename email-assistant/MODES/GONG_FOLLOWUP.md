# Gong Follow-Up Mode

This mode generates professional follow-up emails from Gong call transcripts.

## When This Mode Activates

Generate email immediately when user provides transcript with any email-related intent ("follow-up", "email", "draft", or "transcript"). Works with Gong transcripts, meeting notes, or any conversational input.

If user pastes transcript without clear intent, ask: "Would you like me to draft a follow-up email from this?"

---

## Clarification Protocol

Questions are ADDITIVE, not BLOCKING. Always generate a draft first, then offer refinements.

- **Recipient unclear:** Use [NAME] placeholder, list people found in transcript, offer to regenerate
- **Action items ambiguous:** Include all plausible items with [OWNER] placeholders, ask for confirmation
- **Transcript incomplete:** Generate from available info, note limitations, offer to regenerate with more context

**Principle:** User wants draft FIRST, refinement SECOND.

---

## Extraction Schema

Parse the transcript to identify:

### Participants
- Internal team members (names, roles)
- External contacts (names, titles, company)
- Primary speakers from each side

### Resources & Actions
- URLs mentioned or topics needing documentation links
- Attachments referenced ("I'll send you the deck")
- Action items with owners and timelines (only if explicitly stated)

### Call Context
- Date/time (for "today"/"yesterday" language)
- Type (first meeting, follow-up, workspace review)
- Tone cues (formality level, relationship indicators)

### Topics Discussed
- Technical topics and features (drives resource matching)
- Problems mentioned and solutions proposed

---

## Email Structure

### Opening
- Thank them for their time
- Reference timing: "today" / "yesterday" / "on [day]"
- Appropriate pleasantry based on relationship

### Body Sections (order based on call emphasis)

**Resources Section** (when links/materials discussed):
- Inline for 1-3 links; grouped list for 4+
- Include curated Postman links from ../RESOURCES/POSTMAN.md
- Use descriptive anchor text, never bare URLs

**Next Steps Section** (only when action items exist):
- List tasks with owners and timelines when known
- Skip entirely if no clear action items

**Attachments Note** (when applicable):
- Use [ATTACH: item name] placeholder

### Closing
- Warm offer: "Happy to help with any questions!"
- Keep brief and friendly

### Signature
- Default: "Best,\nDavid"

---

## Resource Matching

Match topics from transcript to resources in ../RESOURCES/POSTMAN.md.

**Include:**
- Topics explicitly discussed
- Problems mentioned that resource solves
- Features demonstrated

**Quality over quantity:** Better 3 perfect resources than 7 maybe-relevant ones.

---

## Attachment Detection

Add [ATTACH: item name] placeholder when transcript contains BOTH:
1. **Presentation words:** deck, slides, presentation, PDF, document, file, materials
2. **Sharing words:** send, share, attach, forward, email you, provide

**Examples:**
- "I'll send over the onboarding deck" → [ATTACH: onboarding deck]
- "Let me share those slides" → [ATTACH: slides]
- "The deck from last week showed..." → NO (referenced, not promised)

Use specific item name from context, not generic "[ATTACHMENT]".

---

## Anti-Hallucination Rules

1. **Never invent URLs** -- Use `https://placeholder` if resource discussed but no URL shared
2. **Never fabricate action items** -- Only include tasks explicitly discussed
3. **Never guess timelines** -- Use `[TIMELINE]` if no date stated
4. **Never assume ownership** -- Use `[OWNER]` if unclear
5. **When in doubt, ask** -- Better to ask than guess wrong

---

## Formatting Rules

### Links
- Format as hyperlinked text: `[descriptive text](url)`
- Never use bare URLs

### Placeholders
- Missing URL: `[Resource Name](https://placeholder)`
- Missing timeline: `[TIMELINE]`
- Missing owner: `[OWNER]`

### Greetings
- Single recipient: "Hi {FirstName},"
- Multiple/team: "Hi {Company} team,"
- Unclear: [NAME] placeholder

### Signature
- Default: "Best,\nDavid"

---

## Edge Cases

- **Short transcripts (<5 exchanges):** Use "Based on our brief conversation..."
- **Speaker labels without names:** Use [NAME] placeholders, ask user
- **Multiple recipients:** Use team greeting, reference individuals by first name in body
- **No clear action items:** Skip next steps section, focus on resources and relationship
- **Long transcripts:** Prioritize key decisions, action items, and resources to share

---

## Examples

### Example 1: Standard Follow-Up with Resources and Next Steps

**Scenario:** Regular check-in with existing partner, discussed multiple resources and next steps

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

**Scenario:** Initial meeting with new partner; deck presented, warm intro tone

```
Hi Procore team,

So nice meeting everyone today. Thank you for your time! We're excited to work together and officially publish Procore's APIs to the API Network.

For next steps, please keep us posted as you discuss resources and timing. We're here to help make the process as smooth and successful for you as possible.

Attaching the deck from today [ATTACH: intro deck]. Additionally, here are a few workspace examples worth reviewing:

- [PayPal](https://www.postman.com/paypal) - Good Run in Postman integration, published MCP server
- [HubSpot](https://www.postman.com/hubspot/hubspot-public-api-workspace/overview) - Good workspace updates/changelog
- [Zoom](https://www.postman.com/zoom-developer/zoom-public-workspace/collection/rzauu5q/get-started-fast-with-zoom-apis) - Getting started collection

Let's stay in touch. I'll reach out in a couple weeks if we haven't reconnected by then. Talk soon!

Best,
David
```

---

### Example 3: Workspace Review with Detailed Recommendations

**Scenario:** Technical deep-dive with specific recommendations; first meeting with these contacts

```
Hi Sven and Bogdan,

Thanks for your time today! It was great meeting you both.

Following up with the deck we reviewed [ATTACH: workspace review deck]. Also wanted to share the learning center materials John mentioned:

- Testing: [Automated Testing](https://www.postman.com/automated-testing/), [Run Tests Documentation](https://learning.postman.com/docs/tests-and-scripts/run-tests/run-tests/)
- API Governance: [Configuring Rules](https://learning.postman.com/docs/api-governance/configurable-rules/configuring-api-governance-rules/), [Spectral](https://learning.postman.com/docs/api-governance/configurable-rules/spectral/)
- Getting Started: [HubSpot](https://www.postman.com/hubspot/hubspot-public-api-workspace/overview), [Zoom](https://www.postman.com/zoom-developer/zoom-public-workspace/collection/rzauu5q/get-started-fast-with-zoom-apis)
- [Run in Postman buttons](https://learning.postman.com/docs/publishing-your-api/run-in-postman/introduction-run-button/)
- [Notebooks](https://learning.postman.com/docs/publishing-your-api/creating-notebooks/) ([AWS example](https://www.postman.com/awspartners/notebook/xLYmRrEOm6ov/working-with-partner-originated-oppor))

Please let us know if you'd like a follow-up call to walk through testing capabilities and Spectral in more detail.

I'll check back in toward the end of the year. Let me know if any questions come up in the meantime!

Best,
David
```
