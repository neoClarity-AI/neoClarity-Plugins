# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is the **Claude plugin marketplace for neoClarity** (`.claude-plugin/marketplace.json`, marketplace name `open-aos-factory`). It contains no application code, build system, or test suite — it is a content repo of markdown specs, YAML catalogs, and Claude skill definitions that together define an installable Claude Code plugin: **AOS Factory** (`claude-plugin/aos-factory`).

There is nothing to build, lint, or test. Changes are validated by reading the rendered files against the schemas/checklists they reference (see below), not by running commands.

## What AOS Factory does

AOS Factory is a Claude Code plugin whose skills interview a user and generate a personal **Agentic Operating System (AOS)** instance: a folder tree of markdown "agents" (each a persona with its own instructions, memory, workflows, templates, configs, and logs) plus global workflows, templates, and an HTML user guide. It ships two skills that are invoked conversationally, not via CLI commands:

- **`skills/build-aos/SKILL.md`** — master builder. Triggered by phrases like "Build my AOS." Creates the full instance folder structure, five required governance agents, at least one optional productive agent, and all global files.
- **`skills/build-agent/SKILL.md`** — generic single-agent engine used both by `build-aos` (to build each required/selected agent) and standalone later (e.g. "Build the Research Agent"). It works for all approved agents by resolving that agent's catalog entry + profile + interview script at build time.

Both skills are strictly **`Proceed`-gated**: they run an interview, preview every file they intend to create/overwrite, and create nothing until the user types the exact string `Proceed`. Any file overwrite is listed and approved separately. Never bypass this gate when authoring or testing skill behavior — it is the core safety mechanism of the design.

## Source-of-truth architecture (important when editing)

This repo is a **rendered copy** of an external design specification (the "Open AOS Factory design specification," referenced throughout as `§<number>` section citations). Concretely:

- `agent-catalog.yaml` — the identity/ownership table for all 15 agents (5 required governance + 10 optional productive): each agent's `domains_owned`, `artifacts_owned`, `inputs`/`outputs`, `collaborates_with` edges, `pre_authorized_actions` vs `approval_required_actions`, and `non_responsibilities`. This is framework-owned and read-only from an instance's perspective.
- `agent-specs/<agent-slug>/profile.md` — behavioral narrative for that agent (how it operates, day-to-day).
- `agent-specs/<agent-slug>/interviews.md` — the scripted Initialization interview for that agent (question content/order/`captures:` targets/`skippable:` defaults are normative; wording may be paraphrased but questions may not be added, removed, or reordered).
- `aos-interviews.md` — the scripted AOS-level setup interview (used by `build-aos`, same normativity rules as above).
- `templates/CLAUDE.md` — the example `CLAUDE.md` that gets copied into a *generated AOS workspace* (not this repo's own CLAUDE.md) during setup.
- `builder-changelog.md` — framework/plugin version history.

**Do not hand-edit generated-looking content to "fix" an agent's behavior** — identity/ownership fields trace to `agent-catalog.yaml`, narrative behavior traces to `profile.md`, and interview scripts are normative. Per the plugin's own contribution rule (see `claude-plugin/aos-factory/README.md`), changes are meant to flow from the upstream design specification (`neoClarity-AI/Open-AOS-Factory`) down into this rendered plugin, not the other way around — so treat asymmetric edits here with suspicion and prefer keeping the two skills' generation rules consistent with the spec sections they cite.

### Governance vs. productive agents

Five governance agents are always built, in this order, before any productive agent: **Security → Memory → Chief of Staff → Review → Feedback**. Ten optional productive agents exist (Tutor, Inbox, Calendar, Task, Project Manager, Research, Writing, Document, Personal CRM, Automation); at least one must be selected — an AOS is never considered fully set up with zero productive agents. Each agent declares `domains_owned` that must stay pairwise disjoint from every other agent (checked at both the framework catalog level and, when building into a real instance, against `/configs/agent-registry.md` and `/aos-map.md`).

### Standard per-agent file set

`build-agent` always generates the same seven files for any agent (paths relative to the AOS instance root, not this repo):

```text
/agents/[agent-name]-agent/[agent-name]-agent.md
/agents/[agent-name]-agent/memory/[agent-name]-memory.md
/agents/[agent-name]-agent/memory/[agent-name]-learnings.md
/agents/[agent-name]-agent/workflows/[agent-name]-primary-workflow.md
/agents/[agent-name]-agent/templates/[agent-name]-output-template.md
/agents/[agent-name]-agent/configs/[agent-name]-config.md
/agents/[agent-name]-agent/logs/[agent-name]-decision-log.md
/outputs/[agent-name]-agent/
```

Agent config files must *reference* `/configs/global-permissions.md` and `/configs/tool-access-matrix.md` rather than restating or overriding them — the tool access matrix (owned by the Security Agent) is the single source of truth for tool access and overrides any per-agent config on conflict.

## Versioning

`claude-plugin/aos-factory/.claude-plugin/plugin.json` pins the plugin `version` (currently `2.2.0`), which must match the `spec_version` cited throughout the skill files and stamped into every generated file's frontmatter. When bumping spec/plugin version, update `plugin.json`, cross-check `spec_version` references in both `SKILL.md` files, and add an entry to `builder-changelog.md`.
