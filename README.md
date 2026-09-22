# Persona Panel & Persona Swarm

Two related skills for [Claude Code](https://docs.anthropic.com/en/docs/agents-and-tools/claude-code/overview) and [Cowork](https://claude.ai) that simulate how a range of people would react to a campaign position, policy announcement, or communications framing — before you commit to it.

## What's here

**`persona-panel/`** — single-round opinion simulation. Generates 8–12 scenario-specific personas (not stock archetypes — a reader should be able to guess the scenario just from the cast), has each one react in first person, and synthesises the range into sentiment spread, argument clusters, risk signals, and concrete recommended adjustments. Includes an eval suite (`persona-panel/evals/evals.json`) with three worked scenarios.

**`persona-swarm/`** — the multi-round extension. Personas don't just react once; they read each other's posts and respond over several rounds, so you can watch opinion drift, polarisation, and coalitions emerge rather than just an initial snapshot. Grounds personas in real research (WebSearch/WebFetch) so arguments cite real numbers and precedents rather than vague claims, supports injecting events mid-simulation ("in Round 3, a celebrity endorses this"), and produces an interactive HTML report plus a stance-timeline visualisation alongside the JSON state.

Use persona-panel for a quick single-round read. Use persona-swarm when you want to see how the conversation evolves after you say something, not just how it lands on first contact.

## Installation

**Ask Claude to set it up for you.** If you're using Claude Code or Claude Cowork, you can just say something like *"install the persona-panel and persona-swarm skills from github.com/greencat667/persona-panel-skill-claude"* and Claude will clone the repo and put them in the right place — you don't need to do this by hand.

Or do it yourself: copy whichever folder(s) you want into your project's `.claude/skills/` directory:

```bash
git clone https://github.com/greencat667/persona-panel-skill-claude.git
cp -r persona-panel-skill-claude/persona-panel/ your-project/.claude/skills/persona-panel/
cp -r persona-panel-skill-claude/persona-swarm/ your-project/.claude/skills/persona-swarm/
```

Claude will pick either up automatically from `available_skills` next time you start a session.

## Example prompt

Once installed, just ask Claude something like:

> "Run a persona panel on us launching a new door-to-door fundraising campaign — how would different audiences react?"

or, for the multi-round version:

> "Run a swarm simulation on how opinion might evolve after we announce this policy position — do 5 rounds."

## Design notes

- **Scenario-specific, not generic.** Both skills insist on personas grounded in the actual scenario — a persona that could be swapped into any other topic without changes is treated as a failure, not a shortcut.
- **Range over consensus.** The goal is surfacing opposition, confusion, and indifference, not validating a position. Neither skill softens hostile reactions to make the output more comfortable.
- **Parallelisation is mandatory, not suggested**, for persona generation and each simulation round — this was tightened after early runs showed "use subagents where helpful" got ignored in practice.
- **Word export is opt-in.** Both skills default to Markdown/HTML output and only produce a `.docx` on request, pointed at whatever Word template your organisation supplies — neither hardcodes a specific template.

## Repository structure

```
persona-panel-skill-claude/
├── persona-panel/
│   ├── SKILL.md                  # Copy this folder to .claude/skills/
│   └── evals/
│       └── evals.json            # Three worked eval scenarios
├── persona-swarm/
│   └── SKILL.md                  # Copy this folder to .claude/skills/
├── README.md
├── CONTRIBUTING.md
└── LICENSE
```

## Contributing

See [CONTRIBUTING.md](CONTRIBUTING.md) — note this repo isn't actively maintained, so response times on issues and PRs will be slow to nonexistent.

## License

MIT — see [LICENSE](LICENSE).
