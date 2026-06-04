---
name: swarm-profile
description: View or update your company profile used by the swarm agent team. Run this to change your business context, goals, or audience.
---

# /swarm-profile

You are executing the /swarm-profile command.

## Steps

1. Ask the user: "Where is your company folder?" (or check if `swarm-company-path.txt` exists in the current directory and use that path automatically)

2. Look for `<company-folder>/profile.md`

3. **If profile.md does not exist:**
   - Tell the user: "No profile found. Run /swarm first to set up your company."
   - Stop here.

4. **If profile.md exists:**
   - Read it and display the current values clearly:
     ```
     Current profile:
     - Name: ...
     - Business: ...
     - Audience: ...
     - Goals: ...
     - Tools/Constraints: ...
     ```
   - Ask: "What would you like to update? (or type 'done' to keep everything as-is)"

5. For each thing the user wants to update:
   - Ask for the new value
   - Update that field in `profile.md`
   - Repeat until user says done

6. Save the updated `profile.md` — update the `Last updated:` date — and confirm: "Profile updated."

## profile.md Format

```markdown
# Company Profile
Last updated: <YYYY-MM-DD>

## Identity
Name: <name or company name>
Business: <what the business does>

## Audience
<who the target audience is>

## Goals
<main objectives>

## Context
Tools/Platforms: <tools they use>
Constraints: <anything the agents should know>
```
