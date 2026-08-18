# Joplin Agent Journal 📓🤖

> **Multi-Agent Journaling System, CLI Logger & Universal Agent Skill for Joplin**

[![Joplin](https://img.shields.io/badge/Joplin-Data_API-blue?logo=joplin)](https://joplinapp.org/)
[![Python](https://img.shields.io/badge/Python-3.8+-green?logo=python)](https://www.python.org/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

`joplin-agent-journal` is a plug-and-play system that allows AI coding assistants and autonomous agents (**Antigravity/AGY**, **Nanobot**, **Hermes Agent**, **Claude Code**, **AutoGPT**, etc.) to log daily session summaries, architectural decisions, and progress reports into [Joplin](https://joplinapp.org/).

---

## 🌟 Key Features

- 🔌 **Plug-and-Play Out-of-the-Box**: Zero complex setup. Includes smart token detection (`JOPLIN_TOKEN` env, `.env`, or Joplin MCP configs).
- 🤖 **Universal Agent Skill (`SKILL.md`)**: Drop-in skill file compatible with Antigravity, Nanobot, Claude Code, Cursor, and Windsurf.
- 📅 **Standardized Journaling**: Enforces `MM-DD-YYYY` date formats and structured Markdown headers.
- 📁 **Automated Notebook Management**: Auto-detects or creates target notebooks (e.g. `Journals`) dynamically.
- 💻 **CLI Tool Included (`joplin-log`)**: Standalone executable CLI tool for humans and terminal scripts.

---

## ⚡ 1-Minute Plug & Play Setup

```bash
# 1. Clone the repository
git clone https://github.com/psthi/joplin-agent-journal.git
cd joplin-agent-journal

# 2. Make joplin-log executable and link it to PATH
chmod +x joplin-log
mkdir -p ~/.local/bin
cp joplin-log ~/.local/bin/

# 3. Log your first journal entry!
joplin-log --token YOUR_JOPLIN_TOKEN -a "User" -t "First Journal Entry" -o "Installed joplin-agent-journal system"
```

*(Retrieve your Web Clipper Token in Joplin under Tools -> Options -> Web Clipper).*

---

## 🤖 Drop-In Agent Integration (`SKILL.md`)

Give your AI agents the power to maintain persistent journal logs by copying [`SKILL.md`](SKILL.md) into your project or agent skills directory:

* **Antigravity / AGY**: Copy `SKILL.md` to `.agents/skills/joplin-journal/SKILL.md`
* **Nanobot**: Copy `SKILL.md` to `.nanobot/workspace/skills/joplin-journal/SKILL.md`
* **Claude Code / Cursor**: Copy `SKILL.md` to `.claude/skills/joplin-journal/SKILL.md`

---

## ⚙️ Configuration (Optional)

You can set your token once via environment variable so you don't have to pass `--token` every time:

```bash
export JOPLIN_TOKEN="your_joplin_web_clipper_token_here"
```

Or create a `.env` file in your home directory (`~/.env`):

```env
JOPLIN_TOKEN=your_joplin_web_clipper_token_here
JOPLIN_API_URL=http://localhost:41184
DEFAULT_NOTEBOOK=Journals
```

---

## 🖥️ CLI Usage Examples

```bash
# Log a session progress entry
joplin-log \
  -a "AGY (Antigravity)" \
  -p "My Project" \
  -t "API Refactoring Complete" \
  -o "Standardized API responses and error handling." \
  --progress "- Replaced legacy REST endpoints\n- Added unit tests" \
  --next "- [ ] Deploy to staging"

# Pipe notes directly from stdin
echo "Completed database migration successfully." | joplin-log -t "DB Migration Notes"
```

---

## 📄 License

Distributed under the MIT License. See `LICENSE` for details.
