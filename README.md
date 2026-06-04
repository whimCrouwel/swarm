# Swarm

A Claude Code plugin that turns any agenda into an autonomous agent team.

Give it a goal. A CEO decomposes it into tasks. You approve the plan. Then worker agents execute in periodic batches — building a persistent company folder that grows smarter over time.

## How It Works

```
/swarm "build a marketing plan"
  → CEO breaks it into tasks → you approve
  → workers run every 10 min automatically
  → company/ folder fills up with real output
  → debrief written to memory when done
```

## Install

```
/plugin marketplace add whimCrouwel/swarm
/plugin install swarm@whimCrouwel/swarm
```

## Commands

| Command | Description |
|---------|-------------|
| `/swarm` | Set up company folder (first run) or check status |
| `/swarm "your agenda"` | Start a new project |
| `/swarm "your agenda" --interval 5` | Start with custom cron interval (minutes) |
| `/swarm-profile` | View or update your company profile |
| `/swarm-stop` | Stop an active swarm |

## First Run

Run `/swarm` with no arguments. Swarm will:
1. Ask where to create your company folder (default: `./company/`)
2. Ask 5 onboarding questions about your business
3. Set up the folder structure and you're ready

## Company Folder

```
company/
├── INDEX.md                 ← lightweight registry (agents read this first)
├── profile.md               ← your business context
├── memory/
│   ├── decisions.md         ← strategic decisions accumulated over time
│   └── learnings.md         ← project debriefs
├── departments/
│   ├── research/            ← research outputs
│   ├── marketing/           ← marketing outputs
│   ├── engineering/         ← engineering outputs
│   └── writing/             ← writing outputs
└── projects/
    └── YYYY-MM-DD-<agenda>/
        └── progress.md      ← task checklist for this project
```

The company folder is yours — open it in Finder anytime to browse what the team has built.

## Agent Team

| Agent | What they do |
|-------|-------------|
| **CEO** | Decomposes agendas, oversees projects, writes debriefs |
| **Researcher** | Competitive analysis, audience research, fact-finding |
| **Marketer** | Copy, campaigns, social media, landing pages, email |
| **Engineer** | Code, system design, tech specs, API design |
| **Writer** | Blog posts, reports, documentation, proposals |

## Token Efficiency

Agents never read the whole company folder. They read `INDEX.md` first (a lightweight registry of every file), then fetch only the specific files they need. One task per cron run — no token blowouts.

## How Cron Works

After you approve a plan, Swarm schedules a cron job (via Claude Code's built-in scheduler). Every N minutes, a fresh agent session fires, picks up the next incomplete task, runs the right worker, and updates `progress.md`. When all tasks are done, the cron cancels itself and writes a debrief to `memory/learnings.md`.

Claude Code must be running for cron to fire. It runs in the background — no terminal needs to stay open.
