# Collecting local Codex and Claude chat data

These steps read local coding-agent transcripts on macOS. (An agent may be prompted to find the commensurate data on Windows.)

## Codex

1. Confirm that local rollouts exist:

   ```sh
   open ~/.codex/sessions
   ```

2. Print the newest main conversation file:

   ```sh
   python3 - <<'PY'
   import json
   from pathlib import Path

   matches = []
   for path in Path.home().glob(".codex/sessions/*/*/*/*.jsonl"):
       try:
           first = json.loads(path.open(encoding="utf-8").readline())
       except (OSError, json.JSONDecodeError):
           continue
       if first.get("payload", {}).get("thread_source") == "user":
           matches.append(path)
   print(max(matches, key=lambda path: path.stat().st_mtime))
   PY
   ```

3. Print the last prompt sent in that conversation:

   ```sh
   python3 - <<'PY'
   import json
   from pathlib import Path

   matches = []
   for path in Path.home().glob(".codex/sessions/*/*/*/*.jsonl"):
       try:
           first = json.loads(path.open(encoding="utf-8").readline())
       except (OSError, json.JSONDecodeError):
           continue
       if first.get("payload", {}).get("thread_source") == "user":
           matches.append(path)

   latest = None
   with max(matches, key=lambda path: path.stat().st_mtime).open(encoding="utf-8") as handle:
       for line in handle:
           try:
               event = json.loads(line)
           except json.JSONDecodeError:
               continue
           payload = event.get("payload", {})
           if event.get("type") == "response_item" and payload.get("type") == "message" and payload.get("role") == "user":
               parts = [item.get("text", "") for item in payload.get("content", []) if item.get("type") == "input_text"]
               if parts:
                   latest = "\n".join(parts)
   print(latest or "No user prompt was found.")
   PY
   ```

Thread names are stored in `~/.codex/session_index.jsonl`. Do not copy `~/.codex/auth.json`.

4. From this repository's root, archive all local Codex sessions and their
   index:

   ```sh
   mkdir -p ai-data-export
   EXPORT_DIR="$PWD/ai-data-export"
   (cd "$HOME/.codex" && zip -r "$EXPORT_DIR/codex-local.zip" sessions session_index.jsonl)
   ```

### Web Account Export

1. Sign in at [chatgpt.com](https://chatgpt.com/).
2. Open the profile menu, then select **Settings → Data controls**.
3. Under **Export data**, select **Export**, then **Confirm export**.
4. Wait for the email or SMS from OpenAI and select **Download data export**.
5. Download the ZIP while signed in to the same account and check it for the
   Codex chats you need.

OpenAI says preparation can take up to seven days and the download link expires after 24 hours. As an alternative, use the [OpenAI Privacy Portal](https://privacy.openai.com/): select **Make a Privacy Request → I have a consumer ChatGPT account → Download my data**.Self-service export availability depends on the plan and workspace; managed Business, Enterprise, and Healthcare workspaces require organization-managed access.

See OpenAI's current [account export instructions](https://help.openai.com/en/articles/7260999-exporting-your-chatgpt-history-and-data) and [Codex chat-history guidance](https://help.openai.com/en/articles/20001333-how-to-archive-and-delete-codex-chats-in-the-chatgpt-app).

## Claude

1. Confirm that local project transcripts exist:

   ```sh
   open ~/.claude/projects
   ```

2. Print the newest main conversation file:

   ```sh
   python3 - <<'PY'
   from pathlib import Path

   matches = [
       path
       for path in Path.home().glob(".claude/projects/**/*.jsonl")
       if "subagents" not in path.parts
   ]
   print(max(matches, key=lambda path: path.stat().st_mtime))
   PY
   ```

3. Print the last prompt sent in that conversation:

   ```sh
   python3 - <<'PY'
   import json
   from pathlib import Path

   matches = [
       path
       for path in Path.home().glob(".claude/projects/**/*.jsonl")
       if "subagents" not in path.parts
   ]
   latest = None
   with max(matches, key=lambda path: path.stat().st_mtime).open(encoding="utf-8") as handle:
       for line in handle:
           try:
               event = json.loads(line)
           except json.JSONDecodeError:
               continue
           if event.get("type") == "queue-operation" and event.get("operation") == "enqueue" and event.get("content"):
               latest = event["content"]
   print(latest or "No enqueued user prompt was found.")
   PY
   ```

Do not copy `~/.claude/.credentials.json`. Claude Desktop metadata under
`~/Library/Application Support/Claude/` may reference these sessions, but it
does not necessarily contain message bodies.

4. From this repository's root, archive all local Claude transcripts and prompt
   history:

   ```sh
   mkdir -p ai-data-export
   EXPORT_DIR="$PWD/ai-data-export"
   (cd "$HOME/.claude" && zip -r "$EXPORT_DIR/claude-local.zip" projects history.jsonl)
   ```

### Web Account Export

For an individual Claude Free, Pro, or Max account:

1. Sign in at [claude.ai](https://claude.ai/).
2. Select your initials in the lower-left corner, then **Settings**.
3. Open **Privacy** and select **Export data**.
4. Wait for the email sent to the address associated with the account.
5. Open the download link while signed in and save the export.

Anthropic says the link expires after 24 hours; request another export if it expires. For Team or Enterprise plans, only the organization's Primary Owner can export organization data. See Anthropic's current [Claude export instructions](https://support.claude.com/en/articles/9450526-export-your-claude-data).