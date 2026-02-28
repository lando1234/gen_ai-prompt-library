---
type: workflow
complexity: expert
context: hybrid
output_format: markdown
interaction_mode: conversational
production_ready: yes
tags: [claude-code, team-management, initiatives, scaffolding, engineering-management, skills-kit, quarterly-planning]
version: 1.0
---

# Setup Team Management Center (Claude Code Skills Kit)

## Usage

Use this skill kit when you need to **bootstrap a complete Management Center workspace** for an engineering team using Claude Code as an AI collaborator.

**Context:**
- You are a tech lead or engineering manager starting in a new machine or workspace
- You want a structured, AI-assisted system for tracking initiatives, quarterly planning, team capacity, and executive updates
- The system needs to be portable — clone a repo, run a skill, done

**Goals:**
- Create a fully structured `management/` directory in any workspace
- Parametrize all team-specific elements (name, mission, Jira key, quarter)
- Install runtime skills (`init-initiative`, `ingest-doc`) that Claude Code will detect and expose as slash commands

**Constraints:**
- Requires Claude Code CLI installed on the target machine
- One-time setup per workspace/machine
- Business-specific content (team members, risk patterns, scope guard) must be completed manually after setup
- `ingest-doc` skill requires Google Drive MCP (`gdrive-reader`) to function

**Prerequisites:**
- Clone `gen_ai-prompt-library` repo on target machine
- Have Claude Code installed

---

## Inputs

| Input | Type | Description | Example |
|---|---|---|---|
| `team_name` | string | Name of the engineering team | `"Platform Engineering"` |
| `team_mission` | string | One-line description of team ownership | `"We provide the core infrastructure for payments"` |
| `jira_key` | string | Jira project key, or "none" | `"PLATFORM"` |
| `jira_domain` | string | Atlassian domain, or "none" | `"mycompany.atlassian.net"` |
| `current_quarter` | string | Active quarter in format QN-YYYY | `"Q1-2026"` |

**Optional Inputs:**
- `base_path`: Destination directory (default: `.` = current directory)

---

## Outputs

**Structure created:**

```
management/
├── CLAUDE.md              ← 11 workflows + state machine + quarter rules
├── team.md                ← skeleton table (complete manually)
├── inbox.md               ← quick capture
├── initiatives/
│   └── _index.md          ← table + initial quarter section
├── templates/
│   ├── initiative.md      ← parameterized with Jira key + quarter
│   ├── resources.md
│   └── dependencies.md
└── .claude/
    └── skills/
        ├── init-initiative/SKILL.md  ← available as /init-initiative
        └── ingest-doc/SKILL.md       ← available as /ingest-doc
```

**Success Criteria:**
- 9 files created with zero unreplaced placeholders
- All `TEAM_NAME`, `JIRA_KEY`, `JIRA_DOMAIN`, `CURRENT_QUARTER` tokens substituted with real values
- Runtime skills detectable by Claude Code from `management/.claude/skills/`

**Failure Modes:**
- `management/` already exists in destination → skill aborts and asks for confirmation
- Jira = "none" → all Jira URL references replaced with "N/A" in generated files
- `gdrive-reader` not available → `ingest-doc` will not function (but setup completes)

---

## Prompt

> This kit is implemented as Claude Code SKILL.md files, not as a single inline prompt.
> The entry point skill (`setup-management`) is stored at:
> `skills/management-center/setup-management/SKILL.md`

**Installation and invocation:**

```bash
# Step 1: Install entry-point skill globally
cp -r skills/management-center/setup-management ~/.claude/skills/

# Step 2: Navigate to target workspace
cd ~/your-workspace

# Step 3: Invoke in Claude Code
/setup-management
```

**What the skill does (in order):**
1. Asks 5 questions (team name, mission, Jira key, Jira domain, quarter)
2. Shows confirmation summary — waits for approval before writing
3. Creates directory structure (`mkdir -p`)
4. Writes `management/CLAUDE.md` with all 11 workflows and parameters substituted
5. Writes 3 template files (initiative, resources, dependencies)
6. Writes skeleton files (team.md, inbox.md, initiatives/_index.md)
7. Writes `init-initiative` and `ingest-doc` SKILL.md into `management/.claude/skills/`
8. Reports all created files and next steps

**Runtime skills (used daily after setup):**

```bash
# From management/ directory in Claude Code:
/init-initiative [initiative-name]   # scaffold new initiative
/ingest-doc [slug]                   # ingest Google Drive docs
```

---

## Workflow Steps

### Step 1: Install (one-time per machine)

- **Action**: `cp -r skills/management-center/setup-management ~/.claude/skills/`
- **Inputs**: Path to cloned `gen_ai-prompt-library`
- **Outputs**: Skill available globally as `/setup-management`

### Step 2: Bootstrap workspace

- **Skill**: `/setup-management`
- **Inputs**: 5 conversational parameters
- **Outputs**: Full `management/` directory with 9 files
- **Success Criteria**: Zero unreplaced placeholders

### Step 3: Complete manual sections

- **Action**: Edit `management/CLAUDE.md` and `management/team.md`
- **Inputs**: Team roster, domain risk patterns, scope guard definition
- **Outputs**: Fully configured CLAUDE.md

### Step 4: Daily use (initiative lifecycle)

- **Decision Point**: New initiative → `/init-initiative`; new docs available → `/ingest-doc`
- **Inputs**: Initiative name or Google Drive docs
- **Outputs**: Scaffolded `initiatives/{slug}/` directory or enriched `initiative.md`

---

## Example Execution

```
User: /setup-management

Skill: Nombre del equipo?
User: Growth Backend

Skill: Misión en una línea?
User: Desarrollamos y mantenemos la infraestructura de crecimiento del ecosistema

Skill: Jira project key?
User: GROW

Skill: Jira domain?
User: mycompany.atlassian.net

Skill: Quarter actual?
User: Q1-2026

Skill: [shows summary] ¿Confirmar?
User: sí

Skill: [creates 9 files]

Management Center creado en ./management/
  management/CLAUDE.md            ✓
  management/team.md              ✓
  management/inbox.md             ✓
  management/initiatives/_index.md ✓
  management/templates/           ✓ (3 files)
  management/.claude/skills/      ✓ (2 skills)
```

---

## Notes

**Kit structure in this library:**
```
skills/management-center/
├── README.md                          ← Installation guide
├── setup-management/SKILL.md          ← Entry point (install globally)
├── init-initiative/SKILL.md           ← Runtime skill (standalone copy)
└── ingest-doc/SKILL.md                ← Runtime skill (standalone copy)
```

**Sync note:** `setup-management` embeds the full content of `init-initiative` and `ingest-doc`. If the runtime skills are updated in `skills/management-center/`, reflect changes in the embedded sections of `setup-management/SKILL.md` as well.

**What the generated CLAUDE.md includes:**
- 11 named workflows (capture, new initiative, review, planning, monthly update, team load, Jira export, Jira sync, quarter carry-over, future quarter)
- Full state machine (6 states: borrador → en refinamiento → planificada → en ejecución → bloqueada → cerrada)
- Quarterly planning rules and carry-over logic
- Critical questions by initiative type (migration, shutdown, cross-domain, campaign)
- Output formats for epics and stories in `plan/`
- Placeholder sections for team-specific scope guard and risk patterns

**Limitations:**
- Daily Jira sync automation (hook-based) is not included — requires MCP Atlassian configuration specific to each organization
- Dashboard web app (if used) requires separate setup
- `ingest-doc` is opinionated toward Google Drive — adapt if using a different document system

**Related:**
- [Guide PRD Creation with Adaptive Workflow](guide-prd-creation-adaptive.md) — pairs well for planning initiatives before `/init-initiative`
