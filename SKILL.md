---
name: joplin-journal
description: Standardized journal logging skill for AI agents (AGY, Nanobot, Hermes, Claude Code, etc.) to record daily project updates, architecture decisions, and session milestones into Joplin.
---

# Joplin Agent Journaling Skill 📓

Use this skill whenever you complete a major milestone, refactor code, make architectural decisions, or end a work session. All entries are recorded into the **Journals** notebook in Joplin using the `MM-DD-YYYY` date standard.

---

## 🛠️ CLI Logging Command (`joplin-log`)

The primary way for agents to log entries is using `joplin-log`:

```bash
joplin-log \
  -a "AGY (Antigravity)" \
  -p "Project Name" \
  -t "Title / Topic" \
  -o "Objective or focus area" \
  --progress "- Bullet points of key progress made" \
  --specs "- Technical details / hardware specs" \
  --next "- [ ] Next action items"
```

### Command Flags:
- `-a`, `--agent`: Agent name (`AGY (Antigravity)`, `Nanobot`, `Hermes`, `User`). Default: `AGY (Antigravity)`.
- `-p`, `--project`: Project name.
- `-t`, `--title`: Short descriptive title for the session/milestone (Required).
- `-b`, `--body`: Markdown content (can also be piped via `stdin`).
- `-o`, `--objective`: Focus area / objective bullet points.
- `--progress`: Key progress bullet points.
- `--specs`: Technical or hardware specifications.
- `--next`: Next steps / action items.
- `-n`, `--notebook`: Target Joplin notebook (Default: `Journals`).
- `--tags`: Additional comma-separated tags.
- `--date`: Date override in `MM-DD-YYYY` format (Defaults to current date `MM-DD-YYYY`).
- `-k`, `--token`: Joplin Web Clipper API Token (Auto-detected if `JOPLIN_TOKEN` env is set).

---

## 🌐 Direct HTTP REST API (Fallback for API-only Agents)

If an agent cannot execute shell commands directly, it can send an HTTP POST request to the local Joplin API:

- **Endpoint**: `http://localhost:41184/notes?token=<YOUR_JOPLIN_TOKEN>`

### Payload Example:
```json
{
  "parent_id": "<NOTEBOOK_ID>",
  "title": "08-08-2026 - [Project Name] Session Summary",
  "body": "# 📅 Journal Entry: Session Summary\n**Date**: 08-08-2026  \n**Agent**: Hermes  \n**Project**: Project Name  \n**Tags**: `#journal` `#project-name` `#agent-hermes`  \n\n---\n\n### 💡 Progress & Key Changes\n- Refactored API routing.\n"
}
```

---

## 📋 Markdown Template Guidelines

When creating a journal entry, structure the markdown body according to this format:

```markdown
# 📅 Journal Entry: <Title>
**Date**: MM-DD-YYYY  
**Agent**: <Agent Name>  
**Project**: <Project Name>  
**Tags**: `#journal` `#<project-tag>` `#agent-<agent>`  

---

### 🎯 Objective / Focus Area
- Main goal of the session.

### 💡 Progress & Key Changes
- Detailed bullet points of work completed.

### 🛠️ Hardware & Technical Specs
- Technical details, dependencies, or paths.

### 📌 Next Steps & Action Items
- [ ] Task 1
- [ ] Task 2
```

---

## 📌 Standard Tags
- `#journal`
- `#agent-antigravity`
- `#agent-nanobot`
- `#agent-hermes`
