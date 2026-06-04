---
name: swarm-stop
description: Stop the active swarm cron. Cancels the scheduled agent runs and writes a partial debrief to memory.
---

# /swarm-stop

You are executing the /swarm-stop command.

## Steps

1. Use `CronList` to find any active swarm cron jobs (look for jobs with "swarm executor" in the prompt)

2. If no active cron found:
   - Tell the user: "No active swarm found."
   - Stop here.

3. If a cron is found:
   - Read the company folder path from the cron prompt
   - Find the active `progress.md` in that company folder (the one with `Status: in_progress`)
   - Use `CronDelete` to cancel the cron job using the Cron ID stored in progress.md
   - Update `progress.md`:
     - Change `Status: in_progress` → `Status: stopped`
     - Add log entry: `<YYYY-MM-DD HH:MM> Swarm stopped by user`
   - Dispatch CEO agent to write a partial debrief to `memory/learnings.md`:
     - Pass the progress.md content so CEO knows which tasks completed and which did not
     - CEO should note it was stopped early and what remains
   - Update INDEX.md: change the project entry's status from `in_progress` to `stopped`
   - Tell the user: "Swarm stopped. <N> of <total> tasks were completed. Partial debrief written to memory/learnings.md."
