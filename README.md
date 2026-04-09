# kilocode-agents

Prompt and agent presets for building stronger multi-agent workflows in Kilocode.

This repository packages:
- `old-prompt.md`: the original, stricter system prompt draft
- `new-prompt.md`: a revised Kilocode-native system instruction with better delegation and tool guidance
- `agents.json`: a full config bundle for a company-style agent setup centered on a `ceo` orchestrator
- `agent-imports/`: individual `.agent.json` files for one-by-one import in the Kilocode Agents UI

## What This Repo Is For

These files are designed for people who want a more opinionated multi-agent setup in Kilocode, especially for:
- starting a greenfield project with stronger orchestration
- continuing work in a large or unfamiliar codebase
- experimenting with a CEO-style top-level agent that delegates to specialists

The current agent set is intentionally lean rather than overly role-heavy. The goal is robustness, not theater.

## Files

### Prompt Files

- `old-prompt.md`
  Legacy prompt draft. Strong and ambitious, but more rigid and less aligned with Kilocode's actual runtime model.

- `new-prompt.md`
  Revised system instruction tuned for Kilocode's real capabilities:
  - grounded tool usage
  - deliberate delegation
  - skills and MCP awareness
  - safer and more practical orchestration rules

### Agent Files

- `agents.json`
  Full config bundle containing:
  - `default_agent: "ceo"`
  - an `agent` object with the CEO plus supporting subagents

- `agent-imports/*.agent.json`
  Single-agent files matching the current Kilocode per-agent import flow.

Included agents:
- `ceo`
- `product-manager`
- `repo-explorer`
- `architect`
- `lead-engineer`
- `debugger`
- `qa-reviewer`
- `devops-engineer`

## How To Use

### Option 1: Import The Full Config Bundle

Use this when you want the whole company-style setup at once.

In Kilocode:
1. Open `Settings`
2. Go to `About`
3. Click `Import Settings`
4. Select `agents.json`
5. Save the config

Notes:
- This is the correct path for `agents.json`
- The full settings import merges onto your existing config
- Unknown top-level keys such as `$schema` are ignored by the importer

### Option 2: Import Individual Agents

Use this when you want to test or install agents one by one.

In Kilocode:
1. Open `Settings`
2. Go to `Agent Behaviour`
3. Open the `Agents` sub-tab
4. Click `Import`
5. Select one file from `agent-imports/`

Important:
- The current Agents import UI accepts one agent JSON at a time
- It does not import a whole bundle like `agents.json`

## Suggested Workflow

If you want the closest thing to the intended setup:
1. Import `agents.json` through `About -> Import Settings`
2. Read `new-prompt.md`
3. Use the ideas in `new-prompt.md` as the basis for your primary-system instruction or a custom mode prompt
4. Keep `old-prompt.md` around as a comparison point when testing behavior differences

## Design Notes

The agent organization is built around a simple rule:
- the `ceo` should orchestrate, not act like a glorified general-purpose coder

That means:
- the CEO mostly reads, plans, delegates, tracks, and synthesizes
- implementation and diagnosis live in specialist subagents
- review and validation are separated from implementation
- the role set stays small enough to remain reliable

This setup is meant to be more practical than a large "software company simulation" with too many thinly differentiated roles.

## Limitations

- Kilocode's current UI importer for Agents is single-agent only
- Real behavior still depends on your chosen model, enabled tools, permissions, skills, and MCP servers
- You may want to tune prompts, permissions, or model assignments for your own workflow

## License

MIT. See `LICENSE`.
