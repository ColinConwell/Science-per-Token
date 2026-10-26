**AI Data Collection Quick Guide**

Create one folder named `ai-data-export` in your Documents folder. Save every export and copied data folder under that root using the destinations below.

# OpenAI

## Account export — ChatGPT and Codex Web/Desktop App

### All operating systems

1. Sign in to [ChatGPT](https://chatgpt.com/).
2. Open **Profile → Settings → Data controls**.
3. Under **Export data**, select **Export**, then **Confirm export**.
4. Download the ZIP from the email or SMS message within 24 hours.
5. Save it as:

   ```text
   ai-data-export/openai/account-export/chatgpt-export.zip
   ```

6. Extract a copy into the same folder. Keep the original ZIP unchanged.

If the export control is unavailable, use the [OpenAI Privacy Portal](https://privacy.openai.com/) and select **Make a Privacy Request → I have a consumer ChatGPT account → Download my data**.

Source: [Exporting ChatGPT history and data](https://help.openai.com/en/articles/7260999-how-do-i-export-my-chatgpt-history-and-data)

## Codex CLI

### macOS and Linux

1. Stop all Codex sessions.
2. Run:

   ```sh
   EXPORT_ROOT="$HOME/Documents/ai-data-export"
   CODEX_DATA_DIR="${CODEX_HOME:-$HOME/.codex}"
   mkdir -p "$EXPORT_ROOT/openai/codex-local"
   cp -a "$CODEX_DATA_DIR/sessions" "$EXPORT_ROOT/openai/codex-local/" 2>/dev/null || true
   cp -a "$CODEX_DATA_DIR/archived_sessions" "$EXPORT_ROOT/openai/codex-local/" 2>/dev/null || true
   cp -a "$CODEX_DATA_DIR/session_index.jsonl" "$EXPORT_ROOT/openai/codex-local/" 2>/dev/null || true
   cp -a "$CODEX_DATA_DIR/history.jsonl" "$EXPORT_ROOT/openai/codex-local/" 2>/dev/null || true
   ```

3. Do not copy `auth.json`.

### Windows PowerShell

1. Stop all Codex sessions.
2. Run:

   ```powershell
   $ExportRoot = Join-Path $env:USERPROFILE "Documents\ai-data-export"
   $CodexDataDir = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE ".codex" }
   $Destination = Join-Path $ExportRoot "openai\codex-local"
   New-Item -ItemType Directory -Force -Path $Destination | Out-Null

   @("sessions", "archived_sessions") | ForEach-Object {
     $Source = Join-Path $CodexDataDir $_
     if (Test-Path $Source) { Copy-Item $Source $Destination -Recurse }
   }

   @("session_index.jsonl", "history.jsonl") | ForEach-Object {
     $Source = Join-Path $CodexDataDir $_
     if (Test-Path $Source) { Copy-Item $Source $Destination }
   }
   ```

3. Do not copy `auth.json`.
4. If Codex ran in WSL, repeat the Linux steps inside WSL.

Source: [Codex rollout recorder](https://github.com/openai/codex/blob/main/codex-rs/rollout/src/recorder.rs)

## Codex IDE Extension — VS Code or Cursor

### All operating systems

1. Close the IDE.
2. Follow the **Codex CLI** copy steps above. The extension normally uses the same Codex data folder.
3. If the extension ran through SSH, WSL, a dev container, or a VM, repeat the copy on that remote system.
4. For any Codex session visible in VS Code’s native Chat view, also use **Chat: Export Chat...** and save the JSON under:

   ```text
   ai-data-export/openai/codex-ide-exports/
   ```

## Billing and receipts

### ChatGPT subscription

1. Open **ChatGPT → Settings → Account → Payment → Manage**.
2. Open **Invoice History**.
3. Download each invoice or receipt into:

   ```text
   ai-data-export/openai/billing/chatgpt/
   ```

For ChatGPT Business, use **Workspace settings → Billing → Invoices**.

Source: [Finding past ChatGPT invoices](https://help.openai.com/en/articles/12356340-how-can-i-find-my-past-chatgpt-invoices/)

### OpenAI API

1. Open the OpenAI Platform **Billing** page.
2. Download available payment-history records or invoices.
3. Open **Usage**, choose the required date range, and select **Export**.
4. Export **Cost data** and **Activity data** as separate CSV files.
5. Save everything under:

   ```text
   ai-data-export/openai/billing/api/
   ```

Sources: [API invoices and payment history](https://help.openai.com/en/articles/6640792-when-can-i-expect-to-receive-my-openai-api-invoice), [usage and cost exports](https://help.openai.com/en/articles/20001072)

# Anthropic

## Account export — Claude Web/Desktop App

### All operating systems

1. Open Claude on the web or in Claude Desktop.
2. Select **Initials/Name → Settings → Privacy → Export data**.
3. Download the archive from the email within 24 hours.
4. Save it as:

   ```text
   ai-data-export/anthropic/account-export/claude-export.zip
   ```

5. Extract a copy into the same folder. Keep the original archive unchanged.

Source: [Export your Claude data](https://support.claude.com/en/articles/9450526-export-your-claude-data)

## Claude Code CLI

### macOS and Linux

1. Export important sessions in Claude Code:

   ```text
   /export /absolute/path/to/ai-data-export/anthropic/readable-sessions/session-name.txt
   ```

2. Stop Claude Code.
3. Copy all local transcripts:

   ```sh
   EXPORT_ROOT="$HOME/Documents/ai-data-export"
   CLAUDE_DATA_DIR="${CLAUDE_CONFIG_DIR:-$HOME/.claude}"
   mkdir -p "$EXPORT_ROOT/anthropic/claude-code-local"
   cp -a "$CLAUDE_DATA_DIR/projects" "$EXPORT_ROOT/anthropic/claude-code-local/"
   ```

### Windows PowerShell

1. Export important sessions with `/export`.
2. Stop Claude Code.
3. Run:

   ```powershell
   $ExportRoot = Join-Path $env:USERPROFILE "Documents\ai-data-export"
   $ClaudeDataDir = if ($env:CLAUDE_CONFIG_DIR) { $env:CLAUDE_CONFIG_DIR } else { Join-Path $env:USERPROFILE ".claude" }
   $Destination = Join-Path $ExportRoot "anthropic\claude-code-local"
   New-Item -ItemType Directory -Force -Path $Destination | Out-Null
   Copy-Item (Join-Path $ClaudeDataDir "projects") $Destination -Recurse
   ```

4. If Claude Code ran in WSL, repeat the Linux steps inside WSL.

Source: [Manage Claude Code sessions](https://code.claude.com/docs/en/sessions)

## Claude Code Web/App Sessions

### All operating systems

1. Run `claude --teleport` locally.
2. Select the web session.
3. After the session downloads, run `/export` to save a readable copy.
4. Repeat the **Claude Code CLI** copy steps to preserve the downloaded JSONL transcript.
5. For Claude Desktop Code-tab sessions that cannot be teleported or exported, copy the visible transcript into:

   ```text
   ai-data-export/anthropic/readable-sessions/
   ```

Source: [Claude Code on the web](https://code.claude.com/docs/en/claude-code-on-the-web)

## Claude Code IDE Extension — VS Code or Cursor

### All operating systems

1. Open **Claude Code → Session history**.
2. Export or resume each required **Local** session with `/export`.
3. In the **Web** tab, open each required cloud session to download it locally, then export it.
4. Close the IDE.
5. Follow the **Claude Code CLI** copy steps above.
6. If the extension ran through SSH, WSL, a dev container, or a VM, repeat the copy on that remote system.
7. For Claude sessions visible in VS Code’s native Chat view, also run **Chat: Export Chat...**.

Source: [Claude Code IDE integration](https://code.claude.com/docs/en/ide-integrations)

## Billing and receipts

### Claude subscription

1. Open **Claude → Settings → Billing**.
2. Under **Invoices**, select **View** for each invoice.
3. Save each record under:

   ```text
   ai-data-export/anthropic/billing/subscription/
   ```

4. Search email for **Your receipt from Anthropic** and save those receipts too.

Source: [Claude paid-plan billing](https://support.claude.com/en/articles/8325618-paid-plan-billing-faqs)

### Claude API or cloud-provider usage

1. Open the Claude Console **Billing** and **Usage** pages.
2. Download or save the required billing and usage records.
3. If Claude Code used Bedrock, Vertex AI, Foundry, or another provider, export invoices and usage from that provider instead.
4. Save records under:

   ```text
   ai-data-export/anthropic/billing/api/
   ```

# Cursor

## Cursor IDE App

### All operating systems

1. Open each important local chat.
2. Right-click the chat tab or open the `⋯` menu.
3. Select **Export Transcript**.
4. Save the Markdown file under:

   ```text
   ai-data-export/cursor/readable-sessions/
   ```

5. Quit Cursor completely before copying its local stores.

Source: [Cursor chat history](https://docs.cursor.com/en/agent/chat/history)

### macOS

Copy these folders when present:

```text
~/Library/Application Support/Cursor/User/globalStorage/
~/Library/Application Support/Cursor/User/workspaceStorage/
~/.cursor/projects/
~/.cursor/chats/
```

Place the copies under `ai-data-export/cursor/local-data/`.

### Linux

Copy these folders when present:

```text
~/.config/Cursor/User/globalStorage/
~/.config/Cursor/User/workspaceStorage/
~/.cursor/projects/
~/.cursor/chats/
```

Place the copies under `ai-data-export/cursor/local-data/`.

### Windows

Copy these folders when present:

```text
%APPDATA%\Cursor\User\globalStorage\
%APPDATA%\Cursor\User\workspaceStorage\
%USERPROFILE%\.cursor\projects\
%USERPROFILE%\.cursor\chats\
```

Place the copies under `ai-data-export\cursor\local-data\`.

Keep `state.vscdb`, its backup, and any adjacent `-wal` or `-shm` files together.

## Cursor CLI

### All operating systems

1. Run `cursor-agent ls` in each environment and save the session list.
2. Resume any important session with `cursor-agent resume` and copy the visible transcript if needed.
3. Stop Cursor CLI.
4. Copy `.cursor/chats` and `.cursor/projects` using the OS-specific paths above.
5. If Cursor CLI ran in WSL, SSH, a container, or a VM, repeat the copy there.

Source: [Using Cursor CLI](https://docs.cursor.com/en/cli/using)

## Cursor Cloud Agents

### All operating systems

1. Open [cursor.com/agents](https://cursor.com/agents).
2. Record each agent’s title, date, repository, branch/PR, and URL.
3. Copy each required visible transcript into Markdown.
4. Save related diffs, commits, branches, pull requests, and generated files.
5. For a complete remote-data request, email `hi@cursor.com` and request access and portability for all Cloud Agent prompts, responses, tool activity, timestamps, and session metadata.
6. Save the response under:

   ```text
   ai-data-export/cursor/cloud-agents/
   ```

Source: [Cursor Privacy Policy](https://cursor.com/privacy)

## Billing and receipts

### All operating systems

1. Open [Cursor Billing](https://cursor.com/dashboard/billing).
2. Select **Manage Subscription**.
3. In the Stripe portal, open billing history.
4. Download each invoice PDF into:

   ```text
   ai-data-export/cursor/billing/
   ```

5. Save the usage page for the same billing periods.

Source: [Cursor invoices](https://prod.cursor.com/help/account-and-billing/invoices)

# GitHub and Microsoft — Visual Studio Code

## VS Code Native Chat/Agents — Copilot, Claude, or Codex

### All operating systems

1. Open the VS Code **Chat** view.
2. Select a session.
3. Open the Command Palette.
4. Run **Chat: Export Chat...**.
5. Save the JSON under:

   ```text
   ai-data-export/vscode/exported-sessions/
   ```

6. Repeat for every required session.
7. For readable copies, right-click inside a session, select **Copy All**, and paste into a Markdown file.

Source: [Manage VS Code agent sessions](https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions)

## VS Code Local App Data

1. Quit VS Code completely.
2. Copy the applicable folder below into `ai-data-export/vscode/local-data/`.

### macOS

```text
~/Library/Application Support/Code/
```

### Linux

```text
~/.config/Code/
```

### Windows

```text
%APPDATA%\Code\
```

If using VS Code Insiders, portable mode, `--user-data-dir`, WSL, SSH, or a dev container, copy that separate user-data location too.

Source: [VS Code user-data roots](https://code.visualstudio.com/docs/setup/portable)

## GitHub account export

### All operating systems

1. Open GitHub **Settings → Account**.
2. Under **Export account data**, select **Start export** or **New export**.
3. Download the emailed archive within seven days.
4. Save it under:

   ```text
   ai-data-export/github/account-export/
   ```

This archive supplements, but does not replace, the VS Code per-session exports.

Source: [Request a GitHub account archive](https://docs.github.com/en/get-started/archiving-your-github-personal-account-and-public-repositories/requesting-an-archive-of-your-personal-accounts-data)

## Copilot billing and usage

### All operating systems

1. Open GitHub **Settings → Billing & Licensing → Payment history**.
2. Open and download each receipt or invoice.
3. Open GitHub Billing **AI usage**.
4. Save or export the relevant Copilot usage period.
5. Store the records under:

   ```text
   ai-data-export/github/billing/
   ```

Sources: [GitHub payment history](https://docs.github.com/en/billing/how-tos/set-up-payment/manage-payment-info), [Copilot AI usage](https://docs.github.com/en/copilot/how-tos/manage-and-track-spending/monitor-ai-usage)

# Final Collection Check

1. Confirm the collection contains these top-level folders:

   ```text
   ai-data-export/
   ├── openai/
   ├── anthropic/
   ├── cursor/
   ├── vscode/
   └── github/
   ```

2. Confirm every account export was downloaded and extracted.
3. Compare each app’s visible session list with the exported files.
4. Confirm local data was copied from every computer, WSL instance, remote host, container, and VM used.
5. Confirm cloud-agent sessions and related repository artifacts were saved.
6. Confirm invoices and usage records cover every billing period needed.
7. Keep all original ZIP, JSONL, JSON, SQLite, CSV, Markdown, and PDF files unchanged.
8. Encrypt the completed `ai-data-export` folder before moving or sharing it.
