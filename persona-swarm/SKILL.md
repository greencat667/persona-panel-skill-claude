---
name: persona-swarm
description: >
  Run a multi-round opinion simulation where AI personas react to each other's posts and update their positions over time. Use this skill whenever someone wants to simulate how public opinion evolves after a campaign launch, policy announcement, or communications intervention — not just initial reactions, but drift, polarisation, and emerging consensus. Triggers on: "run a swarm simulation", "simulate opinion drift", "how would opinions evolve", "multi-round persona simulation", "swarm lite", "run persona rounds", "how does the conversation evolve after", "simulate what happens next", "opinion dynamics on". This is the multi-round extension of persona-panel — use persona-panel for a quick single-round snapshot, use this for watching how the conversation develops over time.
---

# Persona Swarm: Multi-Round Opinion Simulation

A multi-round opinion simulation. Personas don't just react to a scenario in isolation — they read each other's posts and respond. Over several rounds, positions shift, arguments spread, polarisation forms, and unexpected coalitions emerge.

This models how public opinion actually works: people form views partly from the original stimulus and partly from watching how others react to it.

**Default: 5 rounds.** The user can override this (e.g. "run 3 rounds" or "do 8 rounds").

## What makes this different from persona-panel

Persona-panel answers: *how might people initially react?*
Persona-swarm answers: *how does the conversation evolve after you say it?*

The value is in the emergent dynamics: which arguments gain traction, where echo chambers form, who shifts and why, whether consensus or polarisation is the endpoint.

## Step 1 — Read the scenario and set parameters

Read the scenario. Identify:
- What's being tested (the announcement, position, or decision)
- The number of rounds (default 5, override if specified)

If the user provides a JSON state file from a previous persona-panel or persona-swarm run, load it and skip persona generation — go straight to the rounds.

## Step 1b — Research grounding

Before generating personas, **research the real-world context** around the scenario. Use WebSearch and WebFetch to gather:

- **Local news and sentiment** — what are real people, community groups, and local media saying about this issue in the specific geography?
- **Key data points** — statistics, polling, economic figures that would inform real stakeholders' views
- **Precedents** — similar decisions or campaigns elsewhere and their outcomes
- **Stakeholder positions on record** — what have relevant organisations, politicians, unions, industry bodies actually said?

Build a **source library** — a numbered list of URLs with one-line descriptions. Keep it available throughout the simulation. Aim for **8–15 sources** covering multiple angles (pro, anti, technical, community, political). Don't just search for sources that confirm the scenario — include industry and opposition voices too.

These sources serve two purposes:

1. **Grounding personas** — real data points, quotes, and local context make persona responses specific rather than generic. Feed relevant facts into persona prompts so their arguments cite real numbers, real places, real precedents.
2. **Citing the synthesis** — the final report must include inline linked citations (source name hyperlinked in parentheses) for every key data point. This makes the output useful for campaign planning, not just a thought experiment.

If the user provides URLs or documents as part of the scenario, fetch and read those first — they form the seed of the source library. Then expand with additional searches.

## Step 2 — Map audiences and generate personas

Follow the same approach as persona-panel:

1. **Map** who is specifically relevant to this scenario — direct stakeholders, adjacent interests, affected workers, motivated opponents, and disengaged bystanders.
2. **Generate 10 personas** from that map, each with: name, age, occupation, location, core values, media diet, prior stance, attitude anchor.
3. Present the persona table.

Same quality rules apply: personas must be scenario-specific, not stock archetypes. A reader should be able to guess the scenario from the cast.

**Grounding rule:** each persona's attitude anchor and prior stance should reference at least one real data point or local fact from the source library. A retired engineer near a planned site should cite the actual grid numbers. A housing campaigner should reference real waiting list data if available. This specificity is what separates a useful simulation from generic opinion theatre.

## Step 3 — Run Round 1

Round 1 is identical to a persona-panel interview. Each persona writes their initial reaction to the scenario in first person, in their voice. 2–3 paragraphs each.

**Parallelisation is mandatory for Round 1.** Launch **2 agents in parallel** (5 personas each). Do not write any persona responses in the main conversation — delegate all 10 to agents. Collect results from both agents, then write the coordinator summary in the main conversation. This saves ~30 seconds and keeps persona generation out of main context.

**Grounding rule:** persona prompts for Round 1 should include relevant facts from the source library so responses cite real numbers, places, and precedents rather than vague claims.

After Round 1, classify each persona's stance: **supportive / hostile / ambivalent / conflicted**.

Write a brief **Round 1 coordinator summary** (3–5 sentences): the initial spread, the key arguments on each side, and what to watch in the coming rounds.

Then **save state** — write a JSON file to the project's `working/` folder (or the current working directory) called `swarm-state.json`. Format:

```json
{
  "scenario": "description of the scenario being tested",
  "config": { "total_rounds": 5 },
  "sources": [
    { "id": 1, "url": "https://...", "description": "One-line description" }
  ],
  "personas": [
    {
      "id": 1,
      "name": "...",
      "age": 0,
      "occupation": "...",
      "location": "...",
      "values": "...",
      "media_diet": "...",
      "attitude_anchor": "...",
      "stance_history": ["supportive"]
    }
  ],
  "rounds": [
    {
      "round": 1,
      "posts": [
        {
          "persona_id": 1,
          "saw_posts_from": [],
          "response": "full text of their post",
          "stance": "supportive"
        }
      ],
      "coordinator_summary": "..."
    }
  ],
  "events": []
}
```

## Step 4 — Run interaction rounds (Rounds 2–N)

**Parallelisation:** Within each round, persona responses are independent — they all see the same set of previous-round posts. Use subagents to generate them in parallel where available: split the 10 personas across 2–3 subagents (3–5 personas each), provide each subagent with the scenario, the persona cards, and the sampled posts for each persona. Collect results, then run the coordinator summary. Between rounds there is a hard dependency (Round N+1 needs Round N's posts), so rounds must run sequentially.

For each subsequent round:

### 4a. Sample who sees what

Each persona sees **3–4 posts from the previous round** (not all of them — this models partial visibility, like real social media). The sample should be weighted:
- **1–2 posts from personas with similar views** (echo chamber / reinforcement)
- **1–2 posts from personas with opposing or different views** (challenge / conflict)

This weighting is critical. If everyone sees everything, you get fast convergence to artificial consensus. If everyone only sees like-minded posts, you get pure echo chamber. The mix produces the realistic dynamics you're testing for.

### 4b. Generate responses

For each persona, present them with:
- The original scenario (brief reminder)
- The 3–4 posts they're "seeing" this round
- Their own previous post(s)

Then write their new post in first person, in character. Their response should:
- **React to what they saw** — agree, disagree, build on, or dismiss specific arguments from other personas
- **Potentially shift stance** — if an argument would genuinely move this person, let them move. Don't force movement for drama, and don't prevent it for consistency
- **Potentially entrench** — if challenged by an argument that triggers their attitude anchor, they may harden rather than soften
- **Stay in voice** — vocabulary, register, and personality should remain consistent even as positions shift
- **Be 1–3 paragraphs** — shorter than Round 1, since these are reactions to reactions

### 4c. Coordinator summary

After each round, write a brief coordinator summary:
- **Shifts:** Which personas moved position, and why? Name names and cite the specific argument that moved them.
- **Entrenchment:** Who hardened? What triggered it?
- **Traction:** Which arguments are spreading (mentioned by multiple personas who didn't originate them)?
- **Clusters:** Are groups forming? Who's aligned with whom?

Update the JSON state file with the new round data.

### 4d. Presentation in conversation

To keep the conversation readable over 5 rounds:
- **Round 1:** Show all full persona responses (as in persona-panel)
- **Rounds 2–5:** Show the coordinator summary, then highlight **2–3 of the most interesting individual responses** in full (biggest shifts, strongest entrenchment, most unexpected reactions). Don't print all 10 every round — the JSON has the complete record.

## Step 5 — Final synthesis

After all rounds, produce a final synthesis:

### Opinion trajectory
How did the overall sentiment spread change from Round 1 to the final round? Show the shift: e.g. "Round 1: 4 supportive, 3 ambivalent, 3 hostile → Round 5: 5 supportive, 1 ambivalent, 4 hostile — polarisation increased."

### Who moved and why
For each persona who changed stance across the simulation, summarise what moved them and at which round. This is the most actionable section — it shows which arguments actually change minds.

### Arguments that gained traction
Which framings or arguments spread from one persona to others over the rounds? These are the messages that "went viral" in the simulation — worth paying attention to.

### Polarisation map
Where did the panel end up? Are there clear camps? Is there a persuadable middle, or has it collapsed? Did any unexpected alliances form?

### Cascade moments
Were there turning points — a single post or argument that shifted the trajectory? Identify the round and the persona.

### Recommended strategy
Based on the full simulation: 2–3 concrete recommendations for how to manage the conversation after launch. Not just "what to say" but "what to do when X happens in the public conversation."

**Citation rule:** the synthesis must include inline linked citations for every key data point. Format: source name hyperlinked in parentheses — e.g. "Public support for data centres dropped to -24% ([Prospect Magazine](https://...))". In the Word report (.docx), citations must also be real clickable hyperlinks, not plain text. Use OOXML `<w:hyperlink>` elements with relationship IDs in `word/_rels/document.xml.rels`. Display format: `(Source Name — domain.com)` where the parenthesised text is a hyperlink to the full URL. Build a hyperlink registry (URL → rId mapping), a `cite(source_name)` helper that emits the OOXML hyperlink XML, and patch the rels file when assembling the zip. Every polling number, grid statistic, jobs figure, and precedent outcome should be traceable to a named, clickable source.

## Event injection (optional, powerful)

Between any two rounds, the user can inject an event:
- *"Before Round 4, leak a contradictory internal memo"*
- *"In Round 3, a celebrity endorses the campaign"*
- *"After Round 2, local media runs a critical story"*

When an event is injected, present it to all personas at the start of the next round as new information. Log it in the `events` array in the JSON state.

**Interactive mode (default):** When running rounds in the main conversation (round by round), offer event injection after each round: *"Want to inject an event before Round N? Or continue?"* This is where the feature shines — the user can steer the simulation based on what's emerging.

**Batch mode:** When delegating all rounds to a single agent (e.g. for speed), event injection is unavailable — note this when choosing the approach. If the user wants event injection, run interactively.

If no events are injected, just run the rounds straight through.

## Output

Save all output files into whichever project or folder the scenario belongs to:
- If the swarm is run for a specific project, use that project's `outputs/` folder.
- If standalone or ambiguous, ask the user where to save.
- Do **not** default to this skill's own directory — that's where the skill lives, not where output belongs.

Always produce **four things** (fifth is optional):

### 1. Conversation summary
A compressed version in the chat. Show:
- The persona table
- Round 1: coordinator summary only (not all 10 full interviews)
- Rounds 2–5: coordinator summary + 1–2 highlighted quotes per round (biggest shift, strongest entrenchment)
- The full final synthesis

This should be scannable in under 5 minutes.

### 2. Interactive HTML report
Generate a single-file HTML page (using inline CSS and JS, no external dependencies). Filename: `swarm-report-[short-scenario-slug].html`.

The HTML report should include:
- **Header:** scenario description, number of rounds, number of personas
- **Persona cards:** collapsible cards for each persona showing their attributes
- **Stance timeline:** a visual showing each persona's stance across all rounds (use coloured dots or a simple chart — supportive=green, hostile=red, ambivalent=amber, conflicted=purple). This is the centrepiece — it shows the drift at a glance
- **Round-by-round view:** expandable sections for each round showing the coordinator summary and all persona responses for that round
- **Synthesis:** the full final synthesis at the bottom
- **Sources:** the full source library with clickable links

Use Tailwind via CDN (`https://cdnjs.cloudflare.com/ajax/libs/tailwindcss/2.2.19/tailwind.min.css`) for styling. Keep it clean and readable.

Share a `computer://` link to this file at the end of the conversation summary.

### 3. Stance timeline visualisation
Generate a separate single-file HTML page designed as a standalone visual. Filename: `swarm-timeline-[short-scenario-slug].html`.

This is a dark-background data visualisation page (not the full report) with three charts:

1. **Aggregate sentiment shift** — stacked horizontal bar chart, one row per round. Segments coloured by stance (supportive=green, conflicted=purple, ambivalent=amber, hostile=red). Shows the green wave eating the other colours across rounds.
2. **Individual stance trajectories** — one row per persona, with coloured dots for each round connected by lines. Amber dashed lines highlight rounds where a persona's stance changed. Personas sorted by starting stance (supportive at top, hostile at bottom) so the pattern of convergence/divergence is visible at a glance.
3. **Argument traction** — horizontal bar chart showing the 5 most-cited arguments and how many personas cited each by Round 5.

Below the charts, include **cascade moment callouts** — 2-3 highlighted turning points with the persona name, round, and key quote.

Use inline CSS and vanilla JS only (no external dependencies except optionally Tailwind CDN). The page should look good as a screenshot or screen-share — think dark dashboard aesthetic.

Present this file to the user alongside the HTML report.

### 4. JSON state file
Always save `swarm-state.json` alongside the HTML report. This is the raw data layer:
- Can be resumed in a later session ("pick up from Round 3 of this swarm")
- Can be manually edited (swap a persona, change a stance, inject an event)
- Can be fed into future analysis or visualisation tools
- Includes the source library for provenance

**The JSON must include the full `posts` array for every round**, with each persona's complete response text and stance classification. This is non-negotiable — without full posts, the state file cannot be used to resume a swarm or feed into the HTML report. Coordinator summaries alone are not sufficient. When delegating rounds to agents, instruct the agent to return full post text for inclusion in the JSON.

### 5. Branded Word report (.docx) — *optional, on request*
Generate only when the user requests it, or when the swarm will be shared with colleagues. Skip for personal projects, exploratory work, or when the HTML report is sufficient.

When producing the .docx: use your organisation's Word template (unpack → replace document.xml → repack, preserving headers/footers/styles). Use the same Python/zipfile approach as other branded docs.

The Word report should be a concise executive summary covering:
- Scenario (with cited context — every key data point with inline source name and URL)
- Opinion trajectory (with stance shift table)
- Who moved and why (with inline source citations where arguments reference real data)
- Arguments that gained traction (with inline source citations)
- Cascade moments
- Polarisation map
- Recommended strategy
- Source library (each source as a clickable hyperlink, not numbered)
- Appendix: persona cards table

Use the template's existing styles: Title, Heading1, Heading2, Heading3, ListParagraph (numId 5 for bullets). A4 page size. Keep the sectPr with headerReference/footerReference rIds intact.

Filename: `swarm-report-[short-scenario-slug].docx`. Save alongside the HTML report.
