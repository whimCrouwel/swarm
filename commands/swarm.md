---
name: swarm
description: Launch an autonomous agent team for any agenda. On first run, sets up your company folder. With an agenda, CEO decomposes tasks for your approval, then workers execute in periodic batches. Run without arguments to see status.
---

# /swarm

You are executing the /swarm command. Detect which state applies and follow the matching instructions exactly.

---

## State Detection

Read the argument passed to this command (if any).

- **No argument, no company folder known** → go to [STATE: FIRST RUN]
- **No argument, company folder exists** → go to [STATE: STATUS]
- **Argument is a plain agenda string** → go to [STATE: NEW AGENDA]

To check if a company folder exists: look for `swarm-company-path.txt` in the current working directory and read the path from it. If that file doesn't exist, check for a `company/` folder in the current directory with an `INDEX.md` inside.

---

## STATE: FIRST RUN

Run this when no company folder exists yet.

**Step 1: Ask for company folder location**

Say: "Welcome to Swarm! Let's set up your company folder. Where would you like to create it? (Press Enter for default: ./company/)"

Read the user's response. If blank, use `./company/` relative to current working directory. Resolve to an absolute path.

Save the absolute path to `swarm-company-path.txt` in the current working directory.

**Step 2: Create folder structure**

Create these directories and files:
```
<company-path>/
├── INDEX.md          ← see format below
├── profile.md        ← empty, filled in Step 4
├── memory/
│   ├── decisions.md  ← initialized in Step 6
│   └── learnings.md  ← initialized in Step 6
├── departments/
│   ├── research/
│   ├── marketing/
│   ├── engineering/
│   └── writing/
└── projects/
```

**Step 3: Run onboarding**

Ask these questions ONE AT A TIME. Wait for each answer before asking the next.

1. "What's your name or company name?"
2. "What does your business do? (2-3 sentences)"
3. "Who is your target audience?"
4. "What are your main goals right now?"
5. "Any tools, platforms, or constraints the team should know about? (e.g. 'we use Notion', 'Japanese market', 'no paid tools') — type 'none' to skip"

**Step 4: Write profile.md**

```markdown
# Company Profile
Last updated: <YYYY-MM-DD>

## Identity
Name: <answer 1>
Business: <answer 2>

## Audience
<answer 3>

## Goals
<answer 4>

## Context
Tools/Platforms: <answer 5>
```

**Step 5: Write INDEX.md**

```markdown
# Company Index
Last updated: <YYYY-MM-DD HH:MM>

## Profile
- profile.md | "<name> — <one-line business description>" | updated: <YYYY-MM-DD>

## Memory
- memory/decisions.md | "Key strategic decisions" | updated: <YYYY-MM-DD>
- memory/learnings.md | "Accumulated insights across projects" | updated: <YYYY-MM-DD>

## Research

## Marketing

## Engineering

## Writing

## Projects
```

**Step 6: Initialize memory files**

Write `memory/decisions.md`:
```markdown
# Strategic Decisions
```

Write `memory/learnings.md`:
```markdown
# Project Learnings
```

**Step 7: Confirm and prompt**

Say:
```
Company folder set up at: <path>

Your swarm team is ready. Run `/swarm "your agenda"` to start your first project.
Run `/swarm-profile` anytime to update your business context.
```

---

## STATE: STATUS

Run this when /swarm is called with no argument and a company folder exists.

**Step 1: Find company folder**

Read `swarm-company-path.txt` in the current directory to get the absolute path.

**Step 2: Read INDEX.md**

Read `<company-path>/INDEX.md`.

**Step 3: Display status**

```
Swarm Status — <company name from INDEX.md Profile section>
Company folder: <path>

Active projects:
  <list projects with "in_progress" status from INDEX.md>

Completed projects:
  <list projects with "completed" status from INDEX.md>

Recent outputs:
  <last 5 file entries from INDEX.md across all department sections>

Run `/swarm "your agenda"` to start a new project.
Run `/swarm-profile` to update your company profile.
```

If no projects exist yet, say: "No projects yet. Run `/swarm "your agenda"` to start your first."

---

## STATE: NEW AGENDA

Run this when /swarm is called with an agenda string.

**Step 1: Find company folder**

Read `swarm-company-path.txt`. If not found, check for `./company/INDEX.md`. If still not found:
- Say: "No company folder found. Run `/swarm` without arguments to set one up."
- Stop here.

**Step 2: Check for already active project**

Read INDEX.md. If any project entry contains `in_progress`:
- Say: "There is already an active project. Run `/swarm-stop` first, or run `/swarm` to check status."
- Stop here.

**Step 3: Read context files**

Read `<company-path>/profile.md` and `<company-path>/INDEX.md`.

**Step 4: Dispatch CEO agent**

Dispatch the CEO agent with:
- The contents of profile.md
- The contents of INDEX.md
- The user's agenda
- Instruction: "Decompose this agenda into 3-8 tasks and present the plan to the user for approval. Do NOT write progress.md yet — just present the plan and ask: 'Approve this plan? (yes / edit / cancel)'"

**Step 5: User approval loop**

After CEO presents the plan:
- If user says **cancel**: stop, do nothing.
- If user says **edit**: ask what to change, re-dispatch CEO with the changes, loop back.
- If user says **yes**: continue to Step 6.

**Step 6: Determine cron interval**

Check if the user passed `--interval <N>` in their original command (e.g. `/swarm "agenda" --interval 5`). If yes, use that number.

Otherwise ask: "How often should the team check for new work? (default: 10 min — press Enter to accept, or type a number)"

Default to 10 if blank.

**Step 7: Create project slug**

Slug format: `<YYYY-MM-DD>-<first-5-words-of-agenda-in-kebab-case>`
Example: `2026-06-04-build-marketing-plan-for`

**Step 8: Dispatch CEO to write progress.md**

Dispatch CEO agent with instruction to:
1. Create directory `<company-path>/projects/<slug>/`
2. Write `<company-path>/projects/<slug>/progress.md` with the approved task list
3. Update INDEX.md by adding under `## Projects`: `- projects/<slug>/progress.md | "status: in_progress | tasks: 0/<N>" | <date>`

**Step 9: Schedule cron**

Use CronCreate with:
- Schedule: `*/<interval> * * * *`
- Prompt (fill in the actual values for company-path and slug):

```
You are the swarm executor. Your job:
1. Read <company-path>/projects/<slug>/progress.md
2. Find the FIRST task marked [ ] (incomplete)
3. Based on the [Dept] tag, dispatch the matching agent:
   - [Research] → researcher agent
   - [Marketing] → marketer agent
   - [Engineering] → engineer agent
   - [Writing] → writer agent
4. Pass the agent: the task description, the company folder path (<company-path>), and instruction to read INDEX.md first then do the work then update INDEX.md
5. After the agent completes, update progress.md: change [ ] to [x] on that task and append the output file path, add a log entry with timestamp
6. Check if ALL tasks are now [x]. If yes: dispatch CEO agent to write debrief (read all [x] task outputs, append to memory/learnings.md and memory/decisions.md if applicable), update INDEX.md project status to "completed", then use CronDelete with the Cron ID stored in progress.md to cancel this cron
```

**Step 10: Write Cron ID to progress.md**

After CronCreate returns, append to progress.md:
```
Cron ID: <returned-cron-id>
```

**Step 11: Confirm to user**

```
Project started: <slug>

The team will check for work every <interval> minutes.
You'll receive a summary when all <N> tasks are complete.

Run `/swarm` to check status anytime.
Run `/swarm-stop` to cancel.
```
