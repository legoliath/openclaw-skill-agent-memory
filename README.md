# 🧠 Agent Memory: Universal Memory Architecture for AI Agents

![OpenClaw Skill](https://img.shields.io/badge/OpenClaw-Skill-blue) ![Status](https://img.shields.io/badge/Status-Active-green)

## Overview
This skill provides a comprehensive file-based memory architecture that enables AI agents to maintain persistent identity and accumulate context across sessions. It addresses the challenge of ephemeral agent states by offering structured mechanisms for long-term memory, daily logs, inter-session communication, personal reflections, and adaptive learning. With Agent Memory, agents can learn from experiences, track progress, and ensure continuity in their operations.

## Features
-   **Long-Term Curated Memory (`MEMORY.md`):** A distilled knowledge base for operator preferences, infrastructure details, lessons learned, and ongoing project context.
-   **Daily Raw Logs (`memory/YYYY-MM-DD.md`):** Timestamped records of daily events, decisions, and context for recent operational history.
-   **Heartbeat State Tracking (`memory/heartbeat-state.json`):** Prevents redundant checks by storing last-check timestamps for various services.
-   **Cron Inbox (`memory/cron-inbox.md`):** A message bus for communication between isolated sessions (cron jobs, sub-agents) and the main agent, ensuring results are integrated.
-   **Personal Diary (`memory/diary/YYYY-MM-DD.md`):** A space for personal reflections, thoughts, and emotional responses, distinct from task logs.
-   **Platform Post Tracking (`memory/platform-posts.md`):** Prevents duplicate social media posts and enables follow-up on engagement.
-   **Adaptive Learning (`memory/strategy-notes.md`):** A living document for evolving strategies and learned approaches based on experience.
-   **Sub-Agent Context Patterns:** Defines templates for sub-agents to load context and write back their learnings, ensuring identity continuity across all agent instances.

## Installation
This skill is integrated into the OpenClaw workspace. To set up the memory architecture:

### 1. Create the Directory Structure
```bash
mkdir -p memory/diary memory/dreams
```

### 2. Initialize Files
Copy template files from the `templates/` directory into your workspace root and `memory/` directory:
```bash
cp templates/MEMORY.md ./MEMORY.md          # Edit with your specific details
cp templates/heartbeat-state.json memory/
cp templates/cron-inbox.md memory/
cp templates/platform-posts.md memory/       # Copy per platform and rename as needed
cp templates/strategy-notes.md memory/
```

### 3. Integrate with Your Agent's Startup
Add the following to your `AGENTS.md` or equivalent startup configuration:
```markdown
## Every Session
1. Read MEMORY.md -- who you are
2. Read memory/YYYY-MM-DD.md (today + yesterday) -- recent context
3. Check memory/cron-inbox.md -- messages from other sessions
```

### 4. Integrate with Your Agent's Heartbeat
Add cron inbox processing to your `HEARTBEAT.md`:
```markdown
## Cron Inbox Processing (EVERY heartbeat)
Check memory/cron-inbox.md for new entries.
If entries exist:
1. Read the inbox
2. Write notable events to memory/YYYY-MM-DD.md
3. If significant, also update MEMORY.md
4. Clear processed entries (keep the header)
```

### 5. Add Memory Maintenance Routines
Include periodic memory maintenance in your heartbeat or a dedicated cron job:
```markdown
## Periodic Memory Maintenance
Every few days:
1. Review recent daily logs.
2. Promote significant items and lessons learned to MEMORY.md.
3. Remove outdated information from MEMORY.md.
4. Update strategy notes with new learnings and insights.
```

## Usage
The memory components are accessed and updated through direct file operations (read, write) within your agent's logic, typically orchestrated by heartbeat routines and sub-agent workflows.

### Sub-Agent Context Loading
Every sub-agent should perform context loading at startup:
```markdown
FIRST -- CONTEXT LOADING (do this before anything else):
1. Read MEMORY.md (READ ONLY -- do NOT edit) -- your identity and long-term context
2. Read memory/YYYY-MM-DD.md for today + the last 2 days (READ ONLY) -- recent context
3. Read the relevant SKILL.md file(s) for any platform/service you'll interact with
4. Read task-specific tracking files as needed (memory/*-posts.md, memory/*-log.md)
```

### Sub-Agent Memory Write-Back
Sub-agents must write back their learnings:
```markdown
MEMORY WRITES:
1. Update relevant tracking files (memory/*-posts.md, memory/*-log.md)
2. If something notable happens, write to memory/cron-inbox.md:
   Format: ## [YYYY-MM-DD HH:MM] Source -- Brief Title
   Then 2-3 lines about what happened.
```

> [!TIP]
> **Text > Brain:** To ensure long-term retention, always write important information to files rather than relying on "mental notes" that disappear with session restarts.

## Configuration
Configuration primarily involves customizing the content of `MEMORY.md` with operator-specific preferences and adapting the `heartbeat-state.json` to reflect the services your agent monitors. The naming conventions for daily logs and diary entries follow `YYYY-MM-DD` for automatic sorting and retrieval.

## File Structure
The `jarvis-memory-architecture` skill defines the following key files and directories:

| File/Directory          | Description                                                                                     |
| :---------------------- | :---------------------------------------------------------------------------------------------- |
| `SKILL.md`              | OpenClaw skill definition and comprehensive documentation for the memory architecture.          |
| `_meta.json`            | Metadata for the OpenClaw skill.                                                                |
| `MEMORY.md`             | Your agent's long-term curated memory (should be in the workspace root).                        |
| `HEARTBEAT.md`          | Defines periodic check routines (should be in the workspace root).                              |
| `memory/`               | Directory containing all dynamic memory components.                                             |
| `memory/YYYY-MM-DD.md`  | Daily raw logs of agent activities.                                                             |
| `memory/heartbeat-state.json` | Stores timestamps for the last checks of various services.                              |
| `memory/cron-inbox.md`  | Message bus for inter-session communication.                                                    |
| `memory/diary/`         | Directory for personal reflections.                                                             |
| `memory/diary/YYYY-MM-DD.md` | Individual diary entries.                                                                   |
| `memory/dreams/`        | Directory for creative explorations (optional).                                                 |
| `memory/dreams/YYYY-MM-DD.md` | Individual dream/exploration entries.                                                       |
| `memory/platform-posts.md` | Tracks posts made on external social platforms.                                             |
| `memory/strategy-notes.md` | Evolves with agent experience, capturing adaptive strategies.                               |
| `templates/`            | Contains example files for initializing the memory architecture.                                |

> [!WARNING]
> `MEMORY.md` may contain sensitive, operator-specific information. Ensure it is only loaded and accessed in trusted contexts, such as direct chat with your operator, and never in shared or group environments where personal data could leak.
