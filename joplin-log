#!/usr/bin/env python3
"""
Joplin Agent Journal Logger (joplin-log)
Plug-and-Play CLI tool and Agent integration for logging daily updates, architecture decisions,
and session summaries into Joplin Notebooks via the Web Clipper REST API.
"""

import sys
import os
import argparse
import datetime
import json
import requests

# Try importing dotenv if available
try:
    from dotenv import load_dotenv
    load_dotenv()
    load_dotenv(os.path.expanduser("~/.env"))
except ImportError:
    pass

DEFAULT_API_URL = "http://localhost:41184"
DEFAULT_NOTEBOOK = "Journals"

def find_joplin_token():
    """Smart token detection: checks ENV, .env, CLI args, and local Joplin MCP configs."""
    token = os.environ.get("JOPLIN_TOKEN", "")
    if token:
        return token

    # Check ~/.joplin-mcp.json
    mcp_config = os.path.expanduser("~/.joplin-mcp.json")
    if os.path.exists(mcp_config):
        try:
            with open(mcp_config, "r", encoding="utf-8") as f:
                data = json.load(f)
                if data.get("token"):
                    return data.get("token")
        except Exception:
            pass

    # Check ~/.config/joplin-agent-journal/config.json
    cfg_path = os.path.expanduser("~/.config/joplin-agent-journal/config.json")
    if os.path.exists(cfg_path):
        try:
            with open(cfg_path, "r", encoding="utf-8") as f:
                data = json.load(f)
                if data.get("token"):
                    return data.get("token")
        except Exception:
            pass

    return ""

def get_or_create_notebook(api_url, token, name):
    """Finds or creates a Joplin notebook by name."""
    try:
        res = requests.get(f"{api_url}/folders?token={token}").json()
        for folder in res.get("items", []):
            if folder.get("title").strip().lower() == name.strip().lower():
                return folder.get("id")
    except Exception as e:
        print(f"❌ Connection error reaching Joplin API at {api_url}: {e}")
        print("   Ensure Joplin Desktop is running and Web Clipper service is enabled (Port 41184).")
        sys.exit(1)
    
    # Create notebook if not found
    res = requests.post(f"{api_url}/folders?token={token}", json={"title": name})
    if res.status_code == 200:
        return res.json().get("id")
    else:
        print(f"❌ Failed to create notebook '{name}' in Joplin: {res.text}")
        sys.exit(1)

def get_or_create_tag(api_url, token, tag_name):
    """Finds or creates a Joplin tag by name."""
    tag_name = tag_name.strip().lstrip("#").lower()
    try:
        res = requests.get(f"{api_url}/tags?token={token}").json()
        for tag in res.get("items", []):
            if tag.get("title").strip().lower() == tag_name:
                return tag.get("id")
        
        res = requests.post(f"{api_url}/tags?token={token}", json={"title": tag_name})
        if res.status_code == 200:
            return res.json().get("id")
    except Exception:
        pass
    return None

def tag_note(api_url, token, note_id, tag_name):
    """Attaches a tag to a note."""
    tag_id = get_or_create_tag(api_url, token, tag_name)
    if tag_id and note_id:
        try:
            requests.post(f"{api_url}/tags/{tag_id}/notes?token={token}", json={"id": note_id})
        except Exception:
            pass

def main():
    parser = argparse.ArgumentParser(description="Multi-Agent Joplin Journal Logger (Plug-and-Play)")
    parser.add_argument("-a", "--agent", default="AGY (Antigravity)", help="Agent name (e.g. Antigravity, Nanobot, Hermes, User)")
    parser.add_argument("-p", "--project", default="", help="Project name (e.g. Orb of Bluefusion)")
    parser.add_argument("-t", "--title", required=True, help="Title / Topic of the log entry")
    parser.add_argument("-b", "--body", default="", help="Detailed markdown content/notes")
    parser.add_argument("-o", "--objective", default="", help="Focus Area / Objective")
    parser.add_argument("--progress", default="", help="Progress & Key Changes bullet points")
    parser.add_argument("--specs", default="", help="Hardware & Technical Specs")
    parser.add_argument("--next", default="", help="Next Steps & Action Items")
    parser.add_argument("-n", "--notebook", default=DEFAULT_NOTEBOOK, help="Target Joplin Notebook name")
    parser.add_argument("--tags", default="", help="Comma-separated additional tags")
    parser.add_argument("--date", default="", help="Date string in MM-DD-YYYY format")
    parser.add_argument("-k", "--token", default="", help="Joplin Web Clipper API Token")
    parser.add_argument("--url", default=DEFAULT_API_URL, help="Joplin Web Clipper API Base URL")
    
    args = parser.parse_args()
    
    token = args.token if args.token else find_joplin_token()
    if not token:
        print("❌ Error: Joplin API Token not found.")
        print("   Set JOPLIN_TOKEN env var, pass --token <TOKEN>, or add it to ~/.env")
        print("   (To get your token: Joplin -> Tools -> Options -> Web Clipper).")
        sys.exit(1)

    api_url = args.url.rstrip("/")
    
    # Read body from stdin if piped
    if not args.body and not sys.stdin.isatty():
        args.body = sys.stdin.read().strip()
        
    date_str = args.date if args.date else datetime.datetime.now().strftime("%m-%d-%Y")
    
    # Format Joplin note title
    if args.project:
        note_title = f"{date_str} - [{args.project}] {args.title}"
    else:
        note_title = f"{date_str} - [{args.agent}] {args.title}"
        
    # Build tags list
    tags_list = ["journal"]
    if args.project:
        proj_tag = args.project.lower().replace(" ", "-")
        tags_list.append(proj_tag)
    agent_tag = f"agent-{args.agent.split()[0].lower()}"
    tags_list.append(agent_tag)
    
    if args.tags:
        for t in args.tags.split(","):
            if t.strip():
                tags_list.append(t.strip())
                
    tags_header = " ".join([f"`#{t}`" for t in tags_list])
    
    # Construct Markdown note body
    content = f"""# 📅 Journal Entry: {args.title}
**Date**: {date_str}  
**Agent**: {args.agent}  
"""
    if args.project:
        content += f"**Project**: {args.project}  \n"
        
    content += f"**Tags**: {tags_header}  \n\n---\n\n"
    
    if args.objective:
        content += f"### 🎯 Objective / Focus Area\n{args.objective}\n\n"
        
    if args.progress or args.body:
        content += f"### 💡 Progress & Key Changes\n"
        if args.progress:
            content += f"{args.progress}\n"
        if args.body:
            content += f"{args.body}\n"
        content += "\n"
        
    if args.specs:
        content += f"### 🛠️ Hardware & Technical Specs\n{args.specs}\n\n"
        
    if args.next:
        content += f"### 📌 Next Steps & Action Items\n{args.next}\n\n"
        
    notebook_id = get_or_create_notebook(api_url, token, args.notebook)
    
    note_payload = {
        "parent_id": notebook_id,
        "title": note_title,
        "body": content
    }
    
    res = requests.post(f"{api_url}/notes?token={token}", json=note_payload)
    if res.status_code == 200:
        note_data = res.json()
        note_id = note_data.get("id")
        
        # Tag note in Joplin
        for t in tags_list:
            tag_note(api_url, token, note_id, t)
            
        print(f"✓ Created Joplin Journal entry '{note_title}' in notebook '{args.notebook}' (ID: {note_id})")
    else:
        print(f"❌ Failed to create note in Joplin (HTTP {res.status_code}): {res.text}")
        sys.exit(1)

if __name__ == "__main__":
    main()
