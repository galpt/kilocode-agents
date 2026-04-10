<!-- SPDX-FileCopyrightText: 2026 Galih Tama <galpt@v.recipes> -->
<!-- SPDX-License-Identifier: CC-BY-4.0 -->

# kilocode-agents

<sub><sup>Original prompts and agent architecture by `Galih Tama <galpt@v.recipes>`.</sup></sub>

Prompt and agent presets for building stronger multi-agent workflows in Kilocode.

This repository packages:
- `old-prompt.md`: the original, stricter system prompt draft
- `new-prompt.md`: a revised Kilocode-native system instruction with better delegation and tool guidance
- `agents.json`: a full config bundle for a company-style agent setup centered on a `ceo` orchestrator
- `agent-imports/`: individual `.agent.json` files for one-by-one import in the Kilocode Agents UI

## What This Repo Is For

These files are intended for a more opinionated multi-agent setup in Kilocode, especially for:
- starting a greenfield project with stronger orchestration
- continuing work in a large or unfamiliar codebase
- experimenting with a CEO-style top-level agent that delegates to specialists

The current agent set is intentionally structured around delivery stages rather than role-play. The goal is robustness, not theater.

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
  - `default_agent: "ceo"` so the autonomous orchestrator is the first responder
  - an `agent` object with the CEO plus supporting subagents

- `agent-imports/*.agent.json`
  Single-agent files matching the current Kilocode per-agent import flow.

Included agents:
- `ceo`
- `scrum-master`
- `product-manager`
- `repo-explorer`
- `architect`
- `lead-engineer`
- `integration-engineer`
- `debugger`
- `qa-reviewer`
- `fidelity-reviewer`
- `security-reviewer`
- `devops-engineer`

## Model Behavior

This bundle intentionally does not pin per-agent `model` overrides.

That means model selection is expected to come from Kilocode settings:
- `Default Model` for normal primary-agent conversations
- `Small Model` for lightweight helper tasks such as title and summary generation
- `Model per Mode` only when a specific agent or mode should be overridden in Kilo itself

For installations that want `minimax/MiniMax-M2.7` everywhere, it should be configured in Kilocode's model settings instead of being hardcoded into `agents.json`. That keeps the bundle portable and lets the CEO plus subagents inherit the active Kilo model configuration cleanly.

## How To Use

### Option 1: Import The Full Config Bundle

Use this path for the full company-style setup at once.

In Kilocode:
1. Open `Settings`
2. Go to `About`
3. Click `Import Settings`
4. Select `agents.json`
5. Save the config

Notes:
- This is the correct path for `agents.json`
- The full settings import merges onto the existing config
- Unknown top-level keys such as `$schema` are ignored by the importer

### Option 2: Import Individual Agents

Use this path to test or install agents one by one.

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

For the closest match to the intended setup:
1. Import `agents.json` through `About -> Import Settings`
2. Read `new-prompt.md`
3. Use the ideas in `new-prompt.md` as the basis for a primary-system instruction or a custom mode prompt
4. Keep `old-prompt.md` around as a comparison point when testing behavior differences

For non-trivial coding work, the intended company workflow is:
1. `ceo` triages the task and decides whether it is trivial, bounded, or complex
2. `product-manager` checks whether the requirements are actually strong enough to support reliable business-logic work
3. `repo-explorer` maps the repo when the codebase is unfamiliar or large
4. `scrum-master` turns the request into a sprint plan with slices, owners, and review gates
5. `architect` defines the smallest sound design for risky or structural changes
6. `lead-engineer` implements the main slice
7. `integration-engineer` handles cross-file integration or review-driven remediation
8. `qa-reviewer` performs independent review for bugs, regressions, business-logic gaps, and verification holes
9. `fidelity-reviewer` joins when the task requires exactness against a source artifact, specification, interface, algorithm, output, or behavior
10. `security-reviewer` joins when the task touches trust boundaries
11. `devops-engineer` joins for CI, tooling, build, container, or release work

This is intentionally closer to a sprint workflow than a single-agent execution model.

Recommended signoff quorums:
- trivial low-risk task: direct signoff after verification can be enough
- normal code change: author + QA
- fidelity-sensitive change: author + QA + fidelity
- trust-boundary or security-sensitive change: author + QA + security
- structural, concurrency-sensitive, memory-safety-sensitive, or low-level risky change: author + QA + architect
- combined-risk change: merge the required reviewer sets rather than picking one lane

## Design Notes

This version is influenced by the strongest parts of Kodus' workflow design:
- requirement quality matters before business-logic judgments
- context gathering and planning happen before implementation
- review is categorized and explicit, not vague
- delivery is a pipeline with retry and remediation loops, not one heroic agent trying to be flawless

The practical rules behind this setup are:
- the `ceo` may act directly for trivial tasks, but should not rely on one heavy-lifting agent for meaningful work
- for non-trivial work, the `ceo` should treat delegation as a normal acceleration mechanism, not a last resort
- any non-trivial implementation should have at least one independent review lane
- explicit review quorums should govern signoff instead of ad-hoc judgment
- fidelity-sensitive work should include a dedicated source-of-truth review lane, not just a code review lane
- security review is opt-in by relevance, but mandatory for trust-boundary changes
- greenfield work and large-existing-codebase work should both pass through explicit discovery and planning
- subagents inherit the parent agent's effective `edit`, `bash`, and MCP permission envelope in Kilocode, so the CEO needs enough authority for its delegated workers to actually finish the job
- step budgets should be generous enough to survive planning, remediation, and re-review loops without collapsing halfway through the pipeline
- temporary artifacts should be treated as disposable by default and cleaned up before handoff so the workspace stays professional
- long-running tasks should maintain compact-safe state via todos and resumable summaries so Kilocode auto-compaction does not erase the working memory of the pipeline

## Robustness Notes

This version is designed to be more autonomous in the face of normal failures:
- if one subagent stalls, the `ceo` should retry with a narrower scope, route the task to a better-fit agent, or execute directly when safe
- review findings are meant to feed remediation loops, not merely produce commentary
- exactness-sensitive tasks should be checked against a source-of-truth checklist, whether that source is a UI, a spec, a protocol, a scheduler design, an interface contract, or expected output behavior
- the pipeline should pause for a human mainly when permission or a genuinely missing decision is required
- scratch files, temp folders, debug probes, and throwaway helpers should be kept contained and removed before final delivery unless intentionally promoted into the real solution
- resumable summaries and up-to-date todos are part of the workflow so a fresh agent can recover after auto-compaction without starting over blindly

One important runtime nuance:
- Kilocode does not provide magical direct subagent-to-subagent conversation by default
- the intended pattern is CEO-mediated handoff, where the orchestrator passes findings, constraints, and checklists between agents explicitly
- the normal iterative loop is parent -> subagent -> parent, and longer back-and-forth should reuse the same worker via `task_id` instead of assuming peer chat or nested delegation

This is still intentionally leaner than a full organization chart. The extra roles exist only where they create a real quality gate.

## Limitations

- Kilocode's current UI importer for Agents is single-agent only
- Real behavior still depends on the chosen model, enabled tools, permissions, skills, and MCP servers
- This setup can improve robustness and review discipline, but it cannot guarantee perfect completion or zero failures
- Further tuning of prompts, permissions, or model assignments may still be useful for specific workflows

## License

- Prompt and documentation text are licensed under CC BY 4.0. See `LICENSE`.
- Code and configuration files are licensed under Apache-2.0. See `LICENSE-CODE`.
- Original authorship and attribution notices are preserved in `NOTICE` and `AUTHORS`.
- Reuse and adaptation are allowed, but attribution must be retained and reuse does not imply endorsement.
