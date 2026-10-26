# Collecting local Codex and Claude chat data

These steps read local coding-agent transcripts on macOS. (An agent may be prompted to find the commensurate data on Windows.)

## Codex

Paste the script below into any Terminal window. It first shows the last prompt
in the newest main conversation. If you approve it, the script creates one ZIP
in `~/Downloads/ai-data-export/` containing every local session plus the thread
index. It does not include `~/.codex/auth.json`.

```sh
python3 - <<'PY'
import json
import zipfile
from datetime import datetime
from pathlib import Path

root = Path.home() / ".codex"
help_prompt = """Copy this into Codex:

I am trying to export the local data for this Codex conversation, but an
automated search did not identify the conversation I intended. First clarify
which conversation and data scope I mean. Then inspect the local Codex data
locations on this computer, identify this conversation's transcript and
related non-auth metadata, and give me their exact paths. Do not read, print,
or copy authentication tokens or credential files."""

matches = []
for path in root.glob("sessions/*/*/*/*.jsonl"):
    try:
        first = json.loads(path.open(encoding="utf-8").readline())
    except (OSError, json.JSONDecodeError):
        continue
    if first.get("payload", {}).get("thread_source") == "user":
        matches.append(path)

if not matches:
    print("No main Codex conversations were found.\n\n" + help_prompt)
    raise SystemExit(1)

latest_file = max(matches, key=lambda path: path.stat().st_mtime)
latest_prompt = None
with latest_file.open(encoding="utf-8") as handle:
    for line in handle:
        try:
            event = json.loads(line)
        except json.JSONDecodeError:
            continue
        payload = event.get("payload", {})
        if (
            event.get("type") == "response_item"
            and payload.get("type") == "message"
            and payload.get("role") == "user"
        ):
            parts = [
                item.get("text", "")
                for item in payload.get("content", [])
                if item.get("type") == "input_text"
            ]
            if parts:
                latest_prompt = "\n".join(parts)

print("Step 1: verify the conversation\n")
print(f"Newest conversation: {latest_file}\n")
print("Last prompt:\n" + (latest_prompt or "[No user prompt found]"))
approved = input("\nIs this the conversation you intended? [y/N] ").strip().lower()
if approved not in {"y", "yes"}:
    print("\n" + help_prompt)
    raise SystemExit

print("\nStep 2: create the ZIP")
output_dir = Path.home() / "Downloads" / "ai-data-export"
output_dir.mkdir(parents=True, exist_ok=True)
archive = output_dir / f"codex-local-{datetime.now():%Y%m%d-%H%M%S}.zip"
sources = [root / "sessions", root / "session_index.jsonl"]
with zipfile.ZipFile(archive, "w", zipfile.ZIP_DEFLATED) as bundle:
    for source in sources:
        paths = source.rglob("*") if source.is_dir() else [source]
        for path in paths:
            if path.is_file() and not path.is_symlink() and path.name != "auth.json":
                bundle.write(path, Path("codex") / path.relative_to(root))

print(f"\nCreated {archive}")
PY
```

### Web Account Export

1. Sign in at [chatgpt.com](https://chatgpt.com/).
2. Open the profile menu, then select **Settings → Data controls**.
3. Under **Export data**, select **Export**, then **Confirm export**.
4. Wait for the email or SMS from OpenAI and select **Download data export**.
5. Download the ZIP while signed in to the same account and check it for the
   Codex chats you need.

OpenAI says preparation can take up to seven days and the download link expires after 24 hours. As an alternative, use the [OpenAI Privacy Portal](https://privacy.openai.com/): select **Make a Privacy Request → I have a consumer ChatGPT account → Download my data**. Self-service export availability depends on the plan and workspace; managed Business, Enterprise, and Healthcare workspaces require organization-managed access.

See OpenAI's current [account export instructions](https://help.openai.com/en/articles/7260999-exporting-your-chatgpt-history-and-data) and [Codex chat-history guidance](https://help.openai.com/en/articles/20001333-how-to-archive-and-delete-codex-chats-in-the-chatgpt-app).

## Claude

Paste the script below into any Terminal window. It first shows the last prompt
in the newest main conversation. If you approve it, the script creates one ZIP
in `~/Downloads/ai-data-export/` containing every local project transcript and
the optional prompt-history file. It does not include
`~/.claude/.credentials.json`.

```sh
python3 - <<'PY'
import json
import zipfile
from datetime import datetime
from pathlib import Path

root = Path.home() / ".claude"
help_prompt = """Copy this into Claude:

I am trying to export the local data for this Claude conversation, but an
automated search did not identify the conversation I intended. First clarify
which conversation and data scope I mean. Then inspect the local Claude data
locations on this computer, identify this conversation's transcript and
related non-auth metadata, and give me their exact paths. Do not read, print,
or copy authentication tokens or credential files."""

matches = [
    path
    for path in (root / "projects").glob("**/*.jsonl")
    if "subagents" not in path.parts
]
if not matches:
    print("No main Claude conversations were found.\n\n" + help_prompt)
    raise SystemExit(1)

latest_file = max(matches, key=lambda path: path.stat().st_mtime)
latest_prompt = None
with latest_file.open(encoding="utf-8") as handle:
    for line in handle:
        try:
            event = json.loads(line)
        except json.JSONDecodeError:
            continue
        if (
            event.get("type") == "queue-operation"
            and event.get("operation") == "enqueue"
            and event.get("content")
        ):
            latest_prompt = event["content"]
        elif event.get("type") == "user":
            content = event.get("message", {}).get("content", "")
            if isinstance(content, str) and content:
                latest_prompt = content
            elif isinstance(content, list):
                parts = [
                    item.get("text", "")
                    for item in content
                    if item.get("type") == "text"
                ]
                if parts:
                    latest_prompt = "\n".join(parts)

print("Step 1: verify the conversation\n")
print(f"Newest conversation: {latest_file}\n")
print("Last prompt:\n" + (latest_prompt or "[No user prompt found]"))
approved = input("\nIs this the conversation you intended? [y/N] ").strip().lower()
if approved not in {"y", "yes"}:
    print("\n" + help_prompt)
    raise SystemExit

print("\nStep 2: create the ZIP")
output_dir = Path.home() / "Downloads" / "ai-data-export"
output_dir.mkdir(parents=True, exist_ok=True)
archive = output_dir / f"claude-local-{datetime.now():%Y%m%d-%H%M%S}.zip"
sources = [root / "projects", root / "history.jsonl"]
blocked = {".credentials.json", "credentials.json"}
with zipfile.ZipFile(archive, "w", zipfile.ZIP_DEFLATED) as bundle:
    for source in sources:
        paths = source.rglob("*") if source.is_dir() else [source]
        for path in paths:
            if path.is_file() and not path.is_symlink() and path.name not in blocked:
                bundle.write(path, Path("claude") / path.relative_to(root))

print(f"\nCreated {archive}")
PY
```

Claude Desktop metadata under `~/Library/Application Support/Claude/` may
reference these sessions, but it does not necessarily contain message bodies.

### Web Account Export

For an individual Claude Free, Pro, or Max account:

1. Sign in at [claude.ai](https://claude.ai/).
2. Select your initials in the lower-left corner, then **Settings**.
3. Open **Privacy** and select **Export data**.
4. Wait for the email sent to the address associated with the account.
5. Open the download link while signed in and save the export.

Anthropic says the link expires after 24 hours; request another export if it expires. For Team or Enterprise plans, only the organization's Primary Owner can export organization data. See Anthropic's current [Claude export instructions](https://support.claude.com/en/articles/9450526-export-your-claude-data).