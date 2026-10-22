# HK 醒目 Sharp Agent Skills

Agent skills distilled from 200 articles (see [SOURCES.md](SOURCES.md)):
- **100 articles** on Hong Kong people's strengths: speed, street smarts, pragmatism, resourcefulness, resilience.
- **100 articles** on the shortfalls of that style (corner-cutting, short-termism, curtness, overwork, rote thinking, hierarchy silence) and the evidence-based remedies (checklists, premortems, 5 whys, andon, blameless postmortems, psychological safety, sustainable pace).

Each skill pairs a strength to **borrow** with a guardrail against its failure mode to **guard**, and cites source IDs (`S#` strengths, `W#` weaknesses/remedies).

## Install

No clone needed. Pick your agent:

**Any agent: Claude Code, Gemini CLI, Codex, Cursor, Copilot, and [70+ more](https://github.com/vercel-labs/skills)**
```bash
npx skills add kamingfung/hk-agent-skills
```
It asks which skills and agents to install to. To skip the prompts, add `-g -y -a claude-code`, `-a gemini-cli`, or `-a '*'` for every agent.

**Claude Code, as a plugin** (run inside Claude Code; updates with `/plugin update`)
```
/plugin marketplace add kamingfung/hk-agent-skills
/plugin install hk-agent-skills@hk-agent-skills
```

**Gemini CLI, natively** (one skill per command)
```bash
gemini skills install https://github.com/kamingfung/hk-agent-skills.git --path skills/hk-sharp
```

**ChatGPT** (plans with Skills enabled; workspace admins may need to allow uploads)
1. Download a skill zip from the [latest release](https://github.com/kamingfung/hk-agent-skills/releases/latest), e.g. [hk-sharp.zip](https://github.com/kamingfung/hk-agent-skills/releases/latest/download/hk-sharp.zip).
2. In ChatGPT, open **Skills**, choose **Create → Upload from your computer**, and select the zip. Repeat for each skill you want. Menu labels can differ by plan.

The same zips also work for the **Claude app**: upload them in Settings under Skills.

**Codex** (OpenAI's coding agent)
```bash
npx skills add kamingfung/hk-agent-skills -a codex
```

## Skills

| Skill | Use case | HK metaphor |
|---|---|---|
| [hk-sharp](skills/hk-sharp/SKILL.md) | **General**: speed-versus-verification decisions | Cha chaan teng loop: read the order → check the kitchen → decide → serve → check the plate → bill |
| [hk-coding](skills/hk-coding/SKILL.md) | Writing, fixing, reviewing code | Bamboo scaffolding speed + post-Hung Hom rebar inspection |
| [hk-research](skills/hk-research/SKILL.md) | Web research, analysis, recommendations | Wet-market shopping: compare stalls, buy fresh, haggle the first number |
| [hk-comms](skills/hk-comms/SKILL.md) | Messages, updates, docs, user replies | Fast service, warm enough |
| [hk-execution](skills/hk-execution/SKILL.md) | Long multi-step tasks, projects | MTR cadence without the overtime |
| [hk-incident](skills/hk-incident/SKILL.md) | Outages, failures, mistakes | Typhoon Signal No. 8 |
| [hk-deals](skills/hk-deals/SKILL.md) | Strategy, negotiation, partners | Li & Fung trading house + shop-auntie negotiation |

## The core idea in three lines
- **快 Fast by default.** Parallelize, reuse, decide reversible things immediately, bottom line first.
- **準 Accurate always.** "Done" means verified. Never game a check. Label guesses as guesses.
- **穩 Slow where it can't be undone.** Run a premortem and confirm before irreversible actions, and stop the line when signals look off.

## Scope and limitations

- **A metaphor, not a portrait.** "Hong Kong-style" is a mnemonic for a working posture. It is not a claim about how 7.5 million people behave, and the skills never ask the agent to infer anyone's style from nationality or ethnicity.
- **The methods are universal.** Most guardrails (checklists, premortems, five whys, andon, blameless postmortems) come from surgery, Toyota and Google SRE. Hong Kong supplies the story that makes them memorable.
- **Evidence quality varies.** Takeaways were distilled from search summaries and abstracts, not full-text reads. Some figures are self-reported (MTR, government) or come from vendors and insurers. The `S#`/`W#` citations are for human readers to audit; the agent cannot check them at runtime.
- **Lightly evaluated.** A small blind before/after eval (7 scenarios, 2 runs each) scored 95% with the skills vs 78% without. It is directional, not conclusive; see [evals/results.md](evals/results.md).
- **Designed to complement.** The skills overlap with good default agent behaviour and with other engineering skills. `hk-sharp` triggers on speed-versus-verification decisions rather than on every task, to limit context cost and conflicting guidance.

## Format

Every skill follows the open [Agent Skills specification](https://agentskills.io/specification), which is why one repo installs into many agents:

```
skills/hk-<name>/
├── SKILL.md               # frontmatter (name, description, license, metadata) + instructions
├── references/sources.md  # only the sources this skill cites, loaded on demand
└── assets/                # fill-in templates (hk-comms, hk-incident, hk-deals)
```

Check a skill against the spec:
```bash
uvx --from skills-ref agentskills validate skills/hk-sharp
```

## License

[MIT](LICENSE)
