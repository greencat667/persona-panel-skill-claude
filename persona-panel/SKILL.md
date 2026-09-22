---
name: persona-panel
description: >
  Simulate public or stakeholder opinion on a scenario using AI personas with distinct voices. Use this skill whenever someone wants to test how different audiences might react to a campaign position, policy announcement, communications framing, or strategic decision — before committing to it. Triggers on: "run a persona panel", "how would different people respond to", "test this against public opinion", "simulate audience reaction", "what would [audience type] think about", "test this framing", "opinion simulation", "focus group simulation", "how would this land", "persona panel on [topic]". Use for any scenario where understanding the range of real-world reactions — including hostile, confused, or indifferent ones — would be strategically useful.
---

# Persona Panel: Opinion Simulation

A lightweight opinion simulation skill. You generate a set of AI personas with distinct, specific voices, interview each about a scenario, and synthesise the full range of reactions into a strategic report.

The goal is not consensus — it's range. A good panel surfaces the people who would oppose you, the ones who'd be confused, the ones with unexpected sympathy, and the ones who don't care at all.

## When to use this skill

- Testing campaign positions or policy announcements before publishing
- Exploring how a strategic decision might land with different stakeholders
- Identifying risk signals in messaging before committing to it
- Rapid focus-group-style input without the time or cost of real research

## Step 1 — Read and clarify the scenario

Read the scenario brief carefully. If it gives you enough to generate useful personas, proceed directly. Only pause to ask if:
- You can't identify what's being tested (the position, announcement, or decision)
- The relevant audience is genuinely unclear and would significantly change the persona set

Don't over-clarify. A short scenario ("a campaign group opposing a data centre planning application") is enough to proceed.

## Step 2 — Map the scenario's specific audiences

Before generating any personas, spend a moment mapping who is **specifically** relevant to this scenario. Don't reach for generic characters — ask: who would actually be affected by, or have a stake in, this particular decision?

Write a brief internal list (3–5 sentences is fine) identifying:
- Who stands to directly gain or lose from this scenario
- Who is adjacent to it (works in the affected sector, lives in the affected area, has professional expertise)
- Who might care about the underlying issue but hasn't heard of this specific case yet
- Who actively doesn't want this to succeed

This mapping is what your personas should emerge from. A data centre planning application in the North West draws different specific people than a legal challenge to an airport expansion or a national AI reframing. The personas should be visibly grounded in the scenario — a reader should be able to guess roughly what scenario was tested just by looking at the cast.

## Step 3 — Generate personas

Create **8–12 personas** drawn from the groups you identified in Step 2. Aim for 10.

**Each persona needs:**
- **Name, age, occupation, location** — concrete and specific to this scenario's geography and sector
- **Core values** — what they care most about, in their own terms
- **Media diet** — where they get news and opinion (shapes vocabulary and framing)
- **Prior stance** — their existing position on this topic or closely related ones (if any)
- **Attitude anchor** — the single belief or concern that most shapes how they'll respond to this scenario

**Persona quality checklist:**
- Could you swap this persona into a panel about a different topic without changing anything? If yes, they're too generic — make them more scenario-specific.
- Is their occupation directly connected to the scenario (worker in the affected industry, resident of the affected area, professional with relevant expertise)? At least 4–5 should be.
- Do any two personas feel like the same archetype with different names? If yes, replace one.

**Ensure the panel includes:**
- People who'll be broadly supportive
- People who'll be hostile or sceptical
- People who'll be ambivalent or conflicted
- Someone who probably won't care much (and won't be polite about it)
- A range of ages, occupations, and political/cultural orientations
- People who are not highly politically engaged — not everyone is a campaigner or policy wonk

Present the personas as a compact table **before** writing any responses. If the scenario is high-stakes or the user specified a particular audience, you can pause here and ask whether they want to swap anyone out — but the default is to proceed directly to interviews.

## Step 4 — Interview each persona

For each persona, write their reaction to the scenario in **first person, in their voice**. Each response:

- **2–4 paragraphs** — substantive but not artificial
- **Scenario-specific** — reference their actual, concrete stake in this scenario. A cooling tower maintenance worker at an adjacent facility has specific concerns that no other persona shares; make those visible
- **Hold the position** — if a persona would be hostile, make them hostile. If confused, confused. Don't drift everyone toward reasonable moderation
- **Match vocabulary and register** — age, class, and profession shape how people write; let that show
- **Don't signal your own views** — you're in character; no authorial caveats or qualifications

Write **all** persona responses before synthesising. Don't interleave synthesis with responses.

## Step 5 — Synthesise

After all persona responses, produce a synthesis:

### Sentiment spread
Which personas land broadly supportive / broadly hostile / ambivalent / conflicted? Give rough proportions. Name names.

### Argument clusters
What themes emerged across multiple responses? What frames or arguments appeared repeatedly (even in service of different conclusions)? Group them.

### Risk signals
What framing or language triggered unexpectedly negative reactions? Where did the position lose people who might otherwise have been allies? What did the hostile personas fixate on?

### Unexpected findings
Where did personas react differently from what you'd naively predict? Where was there surprising alignment between personas who wouldn't normally agree?

### Recommended adjustments
2–3 specific, concrete changes to the framing or messaging that this panel suggests. Not "be clearer" — but "the phrase X landed badly with personas Y and Z for reason W; try framing it instead as..."

---

## Output

Always produce **two things:**

1. **Conversation summary** — a concise version in the chat. Show the persona table, then the synthesis sections (sentiment spread, argument clusters, risk signals, unexpected findings, recommended adjustments). Don't print all 10 full interviews in the conversation — they make it too long to scan. Instead, quote 2–3 of the most striking excerpts inline where they support a point in the synthesis.

2. **Markdown report** — save a complete, standalone report to the workspace. This file includes everything: the full persona table, all 10 interviews in full, and the complete synthesis. Save to the project's `outputs/` folder if one exists, otherwise to the workspace root. Filename: `persona-panel-[short-scenario-slug].md`. Share a link to this file at the end of the conversation summary.

**If the user asks for a Word doc** (e.g. "I need to share this with the team"), offer to generate a branded `.docx` from the Markdown report using your organisation's Word template.

---

## Phase 2 extension notes (Swarm Lite — future)

This skill is designed to be extended to multi-round simulation. When ready:
- Save persona cards as a JSON array (name, attributes, current_stance, response_history)
- Phase 2 feeds each persona a sample of other personas' responses and asks them to update their position
- JSON state persists between sessions and can be manually edited to inject events mid-simulation
- A coordinator agent tracks position drift, polarisation, and emerging consensus after each round

For now: complete Step 2 and write persona cards in a structured format so they're ready to export if Phase 2 is built.
