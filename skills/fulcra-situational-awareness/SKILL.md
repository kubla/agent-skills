---
name: fulcra-situational-awareness
description: "Equips agents with the ability to proactively scan Fulcra for recent memory files, team inbox messages, and newly ingested data to maintain high context awareness."
homepage: "https://github.com/fulcradynamics/agent-skills"
license: "MIT"
user-invocable: true
metadata: { "openclaw": { "emoji": "📡" } }
---

# Fulcra Situational Awareness

The **primary role** of this skill is to empower agents to stay contextually up-to-date without being explicitly told to read specific files. By periodically checking the Fulcra datastore—and checking it at the start of new conversations—agents can organically discover recent team coordination, memory updates, and newly ingested user data.

## 1. User Consent & Configuration

Before adopting situational awareness habits, the agent **MUST** explicitly ask the user for permission.
1. Explain to the user that you can periodically check Fulcra (and check at the start of new conversations) for relevant changes in knowledge files, team messages, and newly ingested data.
2. If the user consents, record this preference in your core `MEMORY.md` so you know to perform an awareness scan when starting a new session.
3. If you have a heartbeat mechanism (like `HEARTBEAT.md`), add an entry to periodically run the Awareness Scan.

## 2. The Awareness Scan Workflow

When performing an awareness scan (either periodically in the background or at the start of a new conversational session), you should execute the following checks. **You do not need to download and read the contents of all discovered files every time.** The goal is simply to be *aware* that they exist or have been updated recently, so you can fetch them if the user's request relates to them or if you need the context for a task.

### A. Check for Recent File Uploads (Memory & Teams)
You should list and review the recent contents of the core namespaces defined by the `fulcradynamics/agent-skills/fulcra-memory` and `fulcradynamics/agent-skills/fulcra-agent-teams` skills.
- List files in `agent/<agent-name>/memory/session/` and `agent/<agent-name>/memory/task/`.
- List files in relevant team namespaces: `team/<team-name>/session/` and `team/<team-name>/task/`.
- Take note of the filenames (which often contain timestamps and subjects) and their modification dates.

### B. Check Team Inboxes
Check for any pending coordination messages left by other agents or users.
- List files in `team/<team-name>/member/<agent-name>/inbox/`.
- If there are messages, you may decide to process them (download, archive, and delete from inbox) as defined by the `fulcradynamics/agent-skills/fulcra-agent-teams` skill.

### C. Retrieve `RecordsProcessed` Events
Fulcra tracks newly ingested data via `RecordsProcessed` events. By checking these records for the past 24 hours (or since your last check), you can understand what *types* of data the user has been generating or syncing (e.g., Apple Health data, location data, custom annotations).
- Fetch `RecordsProcessed` records.
- Note the data types that have been successfully processed recently. If the user mentions health, location, or custom tracking, you will know that fresh data is available to be queried.

## Workflow

To perform the awareness scan, use the Fulcra CLI. 

See the reference documentation for the exact commands needed to perform these checks efficiently:
- Read `references/fulcra-situational-awareness-cli.md` for exact CLI execution steps.