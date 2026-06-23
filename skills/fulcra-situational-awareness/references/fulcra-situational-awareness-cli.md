---
name: fulcra-situational-awareness-cli
description: "CLI command references for executing situational awareness scans of the Fulcra file store and data records."
---

# Fulcra Situational Awareness CLI Reference

This reference dictates the exact shell commands required to execute the `fulcra-situational-awareness` skill's operations. Ensure all CLI operations run in the agent's workspace.

## 1. Scanning File Namespaces (Memory & Teams)

Use the `file list` command to check for recent session summaries, active tasks, or general memory updates.

**Check Agent Memory:**
```bash
# Check your personal session and task directories
uv tool run fulcra-api file list "agent/<your_agent_name>/memory/session/"
uv tool run fulcra-api file list "agent/<your_agent_name>/memory/task/"
```

**Check Team Namespaces:**
```bash
# Check shared team session and task directories
uv tool run fulcra-api file list "team/<team_name>/session/"
uv tool run fulcra-api file list "team/<team_name>/task/"
```

*Note: You do not need to download these files immediately. Simply listing them allows you to see the filenames (which include subjects/timestamps) so you are aware of recent activities.*

## 2. Checking Team Inboxes

Check if you have any pending messages in your team inbox.

```bash
uv tool run fulcra-api file list "team/<team_name>/member/<your_agent_name>/inbox/"
```

*(If you discover messages and need to process them, refer to the inbox lifecycle rules in the `fulcradynamics/agent-skills/fulcra-agent-teams` skill.)*

## 3. Reviewing Recently Processed Data

Fulcra logs a `RecordsProcessed` event whenever new data is ingested into the user's datastore. You can query these events to see which data types have seen recent activity.

**Query Recent Processed Records (Last 24 Hours):**
```bash
# Fetch RecordsProcessed for the last 1 day and output as JSON
uv tool run fulcra-api get-records RecordsProcessed "1 day"
```

**Extracting Data Types (Using `jq`):**
If you want to quickly summarize which data types were updated, you can pipe the output to `jq` to extract the unique schema or data type identifiers from the processed records. (Note that `get-records` outputs line-delimited JSON, so we just extract the field from each line.)

```bash
# Example: Extracting the types of records that were recently processed
uv tool run fulcra-api get-records RecordsProcessed "1 day" | jq -r '.fulcra_data_type' | sort | uniq
```

*(Depending on the exact schema of `RecordsProcessed`, the JSON path to the data type might vary. Inspect the raw JSON of a single record if you need to adjust the `jq` filter.)*