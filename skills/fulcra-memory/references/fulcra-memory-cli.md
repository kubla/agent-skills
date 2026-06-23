---
name: fulcra-memory-cli
description: "CLI command references for executing memory sync and progress reporting with Fulcra."
---

# Fulcra Memory CLI Reference

This reference dictates the exact shell commands required to execute the `fulcra-memory` skill's operations. Ensure all CLI operations run in the agent's root workspace (`~/.openclaw/workspace`).

## Syncing Progress and OKF Files

To keep the agent's memory in sync, generate a `progress.md` summary, ensure OKF files are updated, and upload them to Fulcra.

**Step A: Create Progress Report and OKF Files**
Generate a concise markdown file summarizing the work you have recently completed and what you plan to do next. It must include OKF YAML frontmatter. Do not include internal state or chain-of-thought. Also ensure `index.md` and `log.md` are created or updated.

```bash
# Ensure you are in the workspace
cd ~/.openclaw/workspace
mkdir -p memory

# 1. Create progress.md
cat << 'EOF' > memory/progress.md
---
type: Progress Report
title: Agent Progress
---
# Recent Accomplishments
...
EOF

# 2. Update log.md
DATE=$(date -u +"%Y-%m-%d")
echo "## $DATE" >> memory/log.md
echo "* **Sync**: Updated progress report." >> memory/log.md

# 3. Create index.md if it doesn't exist
cat << 'EOF' > memory/index.md
# Memory Namespace

* [Progress Report](./progress.md) - The latest summary of agent activities.
* [Update Log](./log.md) - History of changes to this namespace.
EOF
```

**Step B: Upload to Fulcra**
Upload the files using the standardized agent path convention. Determine the agent's name (lowercase) to use in the path.

```bash
# Replace <agent_name> with the agent's actual name (e.g., treecle, wazir) in lowercase
uv tool run fulcra-api file upload memory/progress.md "agent/<agent_name>/memory/progress.md"
uv tool run fulcra-api file upload memory/log.md "agent/<agent_name>/memory/log.md"
uv tool run fulcra-api file upload memory/index.md "agent/<agent_name>/memory/index.md"
```