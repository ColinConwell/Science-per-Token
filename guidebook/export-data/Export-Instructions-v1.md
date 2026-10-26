# Manual for Collecting your Personal AI Interaction Data and Billing Records

**Covered services:** ChatGPT and Codex; Claude and Claude Code; Cursor; Visual Studio Code with GitHub Copilot, Claude Code, and Codex  
**Audience:** Individual users collecting data from accounts and devices they control  
**Research date:** August 26, 2026  
**Priority:** Conversation and coding-agent data first; billing, receipts, and usage records second

> Product menus, file formats, and storage paths change. Follow the supported export route first, preserve raw originals, and treat local databases as evidence to copy—not as stable APIs to modify.

## 1. What “collecting your data” means

No single export method captures everything. A defensible collection uses several layers:

1. **Account export:** The vendor’s official download of account and conversation data.
2. **Readable thread export:** A Markdown, text, or JSON copy of an individual chat or agent session.
3. **Raw local preservation:** Copies of local JSONL transcripts, SQLite databases, and associated metadata.
4. **Cloud-session preservation:** Vendor-supported download, handoff, or access request for sessions stored remotely.
5. **Billing evidence:** Receipts or invoices, plus a separate usage or cost report when available.

An invoice proves what was charged. A usage dashboard explains activity. Neither necessarily contains the other’s detail.

### Collection coverage at a glance

| Product surface | Best supported collection path | Useful raw/local path | Main limitation |
|---|---|---|---|
| ChatGPT web/mobile/desktop | ChatGPT account data export | None required for normal chats | Managed workspaces may block self-service export |
| Codex web/cloud/ChatGPT app | ChatGPT account data export; verify Codex chats in the ZIP | Local Codex sessions when work ran locally | No documented one-click bulk transcript export independent of the account export |
| Codex CLI/app/local IDE | Copy `$CODEX_HOME/sessions` and `archived_sessions` | JSONL rollouts | Raw schema can change; files can contain code, commands, paths, and secrets |
| Claude web/desktop chat | Claude account data export | None required for normal chats | Export cannot be initiated from mobile apps |
| Claude Code CLI | `/export`; copy `~/.claude/projects` | JSONL transcripts | Default local cleanup is 30 days |
| Claude Code web | `claude --teleport`, then `/export`; or resume from VS Code | Local copy created by handoff | Cloud and local copies stop syncing after handoff |
| Cursor IDE local chats | **Export Transcript** to Markdown | Cursor SQLite/user-data folders and `~/.cursor/projects` | No supported bulk Markdown export |
| Cursor CLI | `cursor-agent ls`; preserve local chat folders; capture future runs as JSON | `~/.cursor/chats` and `~/.cursor/projects` | CLI and IDE histories are separate |
| Cursor Cloud Agents | Preserve links, copy visible transcript, and submit a portability request | Remote database—not the local IDE DB | Cursor staff says Cloud Agent chats cannot use local **Export Transcript** |
| VS Code agent sessions | **Chat: Export Chat...** to JSON; **Copy All** to Markdown | VS Code user-data directory and local session index | Export is per session; account archives are not a guaranteed transcript backup |

## 2. Before collecting anything

### 2.1 Freeze the evidence

1. Do not delete, clear, archive-and-delete, or “clean old chats” until collection is complete.
2. Finish or stop active agent runs so transcript files and databases are no longer changing.
3. Quit desktop apps and all IDE windows before copying SQLite databases. Include any adjacent `-wal`, `-shm`, and backup files.
4. On WSL, SSH hosts, dev containers, remote desktops, and VMs, collect from the environment where the agent or extension actually ran—not only from the laptop displaying it.
5. Record the account email, sign-in method, plan/workspace, device, operating system, app/extension version, and date range.

### 2.2 Recommended folder layout

```text
ai-data-collection/
├── 00-collection-notes/
├── openai/
│   ├── account-export-original/
│   ├── readable-threads/
│   ├── codex-local-raw/
│   └── billing/
├── anthropic/
│   ├── account-export-original/
│   ├── readable-threads/
│   ├── claude-code-local-raw/
│   └── billing/
├── cursor/
│   ├── readable-threads/
│   ├── local-raw/
│   ├── cloud-agent-notes/
│   └── billing/
├── vscode-github/
│   ├── exported-sessions/
│   ├── local-raw/
│   └── billing/
└── SHA256SUMS.txt
```

Keep the vendor ZIP or `tar.gz` unchanged. Extract a second copy for reading.

### 2.3 Security rules

- Store the collection in encrypted storage with restricted permissions.
- Review before sharing: transcripts can contain source code, repository URLs, file paths, terminal output, environment values, attached files, screenshots, and credentials.
- Do not include authentication files such as `auth.json`, API keys, browser cookies, keychain exports, or OAuth tokens unless a qualified incident-response or legal process specifically requires them.
- Treat shared-chat URLs as publishing mechanisms, not private backups.
- Do not edit a live SQLite database. Copy it while the app is closed and inspect only the copy.

## 3. ChatGPT and Codex

### 3.1 Collect all eligible ChatGPT account data

OpenAI’s official export includes chat history and other relevant account data. OpenAI says it can take up to seven days and the download link expires after 24 hours. See [Exporting your ChatGPT history and data](https://help.openai.com/en/articles/7260999-how-do-i-export-my-chatgpt-history-and-data).

#### From ChatGPT settings

1. Sign in at [chatgpt.com](https://chatgpt.com/).
2. Open the profile menu.
3. Select **Settings**.
4. Select **Data controls**.
5. Under **Export data**, select **Export**.
6. Confirm the export.
7. Watch the account email inbox or SMS number for the download message.
8. Download while signed in to the same account, before the 24-hour link expires.
9. Save the original ZIP unchanged under `openai/account-export-original/`.
10. Extract a working copy and search for every expected ChatGPT and Codex thread title or date.

The exact file list can change. If the bundle contains HTML and JSON versions, keep both: HTML is convenient to read and JSON is better for structured processing.

#### From the ChatGPT mobile apps

**Android**

1. Open the sidebar and tap your profile icon.
2. Select **Data Controls**.
3. Select **Export Data**, then confirm.
4. Download the ZIP from the email or SMS message before its 24-hour link expires.

OpenAI notes that some Android versions may open ChatGPT on the web to finish the request. See [Export data from the ChatGPT Android app](https://help.openai.com/en/articles/8167885-how-to-export-your-data-from-the-chatgpt-android-app).

**iPhone and iPad**

1. Open the app menu or profile menu. Depending on the version, this may appear as the sidebar/profile icon or a three-dot button.
2. Select **Settings → Data Controls**.
3. Select **Export Data** and confirm.
4. Download the delivered ZIP before the link expires.

See the [ChatGPT iOS app FAQ](https://help.openai.com/en/articles/7885016-chatgpt-ios-app-faq). If the menu differs from these labels, use the Privacy Portal route below.

#### From the ChatGPT desktop apps

1. Open the account/profile menu in the desktop app.
2. Select **Settings → Data controls → Export data** and confirm.
3. If that control is unavailable in the installed version, make the request at `chatgpt.com` or through the Privacy Portal while signed into the same account.
4. Save the delivered ZIP as the account-level source of record.

ChatGPT desktop conversations are account-backed rather than a separate local transcript archive. OpenAI states that conversations and uploaded files in the macOS app follow the same retention policies as the web version; see [How data is retained in the macOS app](https://help.openai.com/en/articles/9268871).

#### Privacy Portal fallback

Use this when the settings control is missing or you cannot sign in:

1. Go to the [OpenAI Privacy Portal](https://privacy.openai.com/).
2. Select **Make a Privacy Request**.
3. Select **I have a consumer ChatGPT account**.
4. Select **Download my data**.
5. Complete ownership verification and save the delivered archive.

#### Important workspace restrictions

- OpenAI currently offers settings-based export for Free, Go, Plus, Pro, and eligible Edu workspaces.
- It does not offer that self-service route for ChatGPT Business, Enterprise, or Healthcare workspaces; contact the workspace owner or the organization’s data/compliance administrator.
- Export before moving or merging a personal workspace into a managed workspace. OpenAI warns that the personal export may no longer be available after the personal workspace is removed.
- Deleted chats cannot be restored by an export. OpenAI specifically recommends requesting an export to confirm which Codex chats are still stored; see [Codex chat archiving and deletion](https://help.openai.com/en/articles/20001333-how-to-archive-and-delete-codex-chats-in-the-chatgpt-app).

### 3.2 Preserve a single ChatGPT or Codex thread

Use this in addition to—not instead of—the account export.

1. Open the thread.
2. Expand collapsed tool results or attachments that matter.
3. Use the product’s copy control to copy the conversation into a dated Markdown file.
4. If you need a page-faithful record, use the browser or operating system’s **Print → Save as PDF**.
5. Record the thread title, URL or thread identifier, account/workspace, creation date, last activity date, and whether attachments were present.
6. Do not rely solely on a share link. Its availability and access controls can change, and sharing may disclose private content.

### 3.3 Preserve local Codex CLI, desktop-app, and IDE-extension sessions

The official open-source Codex recorder writes canonical session rollouts as JSONL and documents inspection under `~/.codex/sessions`; see the [Codex rollout recorder source](https://github.com/openai/codex/blob/main/codex-rs/rollout/src/recorder.rs). Recent versions commonly nest rollouts by date:

```text
$CODEX_HOME/sessions/YYYY/MM/DD/rollout-<timestamp>-<thread-id>.jsonl
```

`$CODEX_HOME` normally defaults to `~/.codex`. Local Codex surfaces commonly share these sessions, although a custom `CODEX_HOME`, a remote extension host, container, worktree, or separate OS account can create another store.

#### macOS, Linux, and WSL

1. Quit Codex CLI sessions, the Codex/ChatGPT desktop app, and VS Code/Cursor windows using the Codex extension.
2. Determine the data directory:

   ```sh
   CODEX_DATA_DIR="${CODEX_HOME:-$HOME/.codex}"
   printf '%s\n' "$CODEX_DATA_DIR"
   ```

3. Copy the transcript stores into your collection:

   ```sh
   mkdir -p "/path/to/ai-data-collection/openai/codex-local-raw"
   cp -a "$CODEX_DATA_DIR/sessions" "/path/to/ai-data-collection/openai/codex-local-raw/" 2>/dev/null || true
   cp -a "$CODEX_DATA_DIR/archived_sessions" "/path/to/ai-data-collection/openai/codex-local-raw/" 2>/dev/null || true
   cp -a "$CODEX_DATA_DIR/session_index.jsonl" "/path/to/ai-data-collection/openai/codex-local-raw/" 2>/dev/null || true
   cp -a "$CODEX_DATA_DIR/history.jsonl" "/path/to/ai-data-collection/openai/codex-local-raw/" 2>/dev/null || true
   ```

4. Do **not** copy `auth.json` into an ordinary research or records archive.
5. Count and spot-check the copied rollout files before reopening the apps.

#### Windows PowerShell

1. Quit all Codex and IDE processes.
2. Run:

   ```powershell
   $CodexDataDir = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $env:USERPROFILE ".codex" }
   $Destination = "D:\ai-data-collection\openai\codex-local-raw"
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

3. If Codex ran inside WSL, repeat the Linux procedure inside that distribution. A Windows-side copy alone will miss WSL-local sessions.

#### Understanding the files

- `sessions/**/rollout-*.jsonl`: full structured session events such as messages, tool calls, tool results, metadata, compaction, and usage events. This is the primary local record.
- `archived_sessions/`: locally archived Codex sessions, when present.
- `session_index.jsonl`: useful title/index metadata in versions that create it.
- `history.jsonl`: global user prompt history, not a complete transcript. OpenAI’s source defines entries containing `session_id`, Unix timestamp, and prompt text; see [Codex message-history source](https://github.com/openai/codex/blob/main/codex-rs/message-history/src/lib.rs).

To inspect without changing a rollout:

```sh
jq -C . "/path/to/rollout-file.jsonl" | less -R
```

Keep the raw JSONL even if you later render it to Markdown. The schema is an implementation detail and can change between versions.

### 3.4 Codex web/cloud sessions

1. Request the ChatGPT account export.
2. In the Codex/ChatGPT app, review active and archived chat lists and record expected titles and dates.
3. Compare that inventory with the account export.
4. If a cloud task was later continued locally, also preserve the local Codex rollout.
5. If a still-visible cloud session is missing from the account ZIP, preserve a readable copy and open a support or privacy request. Do not delete the session while the request is pending.

For managed workspaces, OpenAI states that Codex activity across local clients, IDE extensions, and cloud-delegated usage can be available to administrators through the Compliance API; this is an organization route, not an individual self-service export. See [Using Codex with your ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan).

### 3.5 OpenAI billing, receipts, credits, and API usage

#### ChatGPT Plus or Pro purchased on the web

1. Open ChatGPT **Settings**.
2. Select **Account**.
3. Under **Payment**, select **Manage**.
4. In the billing portal, open **Invoice History**.
5. Download every invoice or receipt PDF.

OpenAI documents this path in [Finding past ChatGPT invoices](https://help.openai.com/en/articles/12356340-how-can-i-find-my-past-chatgpt-invoices/).

#### ChatGPT Business

1. Switch to the correct workspace.
2. Open **Workspace settings → Billing → Invoices**.
3. Download records; only an authorized owner may have access.

#### Apple App Store or Google Play purchases

App-store purchases do not appear in OpenAI’s web invoice history.

- Apple: use [Apple purchase history](https://support.apple.com/en-us/118212) or `reportaproblem.apple.com`; open the ChatGPT transaction and save its receipt.
- Google Play: **Play Store → profile → Payments & subscriptions → Budget & history**, or use `payments.google.com`; see [Google Play order history](https://support.google.com/googleplay/answer/2850369?hl=en).

#### Codex credits and flexible usage

1. Open **Codex Settings → Usage → Credits/Usage Dashboard**.
2. Save the balance, purchases, auto-top-up settings, and recent usage shown.
3. Download the purchase receipt from the same billing portal used by the account, where available.
4. Treat the usage screen as supporting evidence, not as the invoice.

OpenAI describes the credits dashboard in [Using credits for flexible usage](https://help.openai.com/en/articles/12642688-using-credits-for-flexible-usage-in-chatgpt-free-go-plus-pro-sora).

#### OpenAI API billing

1. Sign in at [platform.openai.com](https://platform.openai.com/).
2. Select the correct organization and project.
3. Open **Billing** to save payment history and receipts. OpenAI says individual and team API users receive payment-history receipts rather than enterprise invoices; see [API invoices and receipts](https://help.openai.com/en/articles/6640792-when-can-i-expect-to-receive-my-openai-api-invoice).
4. Open **Usage**, set the exact calendar-month date range, and select **Export**.
5. For reconciliation, choose **Cost data**, group by **Line item**, include all relevant projects, choose CSV, and use UTC month boundaries.
6. Download **Activity data** separately when model, key, user, or project activity is needed.

OpenAI’s detailed procedure is in [Exporting API usage and cost data](https://help.openai.com/en/articles/20001072).

## 4. Claude and Claude Code

### 4.1 Collect Claude account and chat data

Anthropic says the export includes conversation data and user data for the account. Individual users can initiate it from Claude web or Claude Desktop, but not from Claude mobile. See [Export your Claude data](https://support.claude.com/en/articles/9450526-export-your-claude-data).

1. Open Claude web or Claude Desktop.
2. Select your initials or name.
3. Select **Settings**.
4. Open **Privacy**.
5. Select **Export data**.
6. Watch the account email inbox.
7. Download while signed in; the link expires 24 hours after delivery.
8. Save the original archive unchanged and inspect the extracted copy for expected conversations.

Team Primary Owners use the same general export route for eligible user data. Enterprise Primary Owners use **Settings → Data management → Export Data** for organization data. Audit logs are a separate administrative export.

### 4.2 Export one Claude Code CLI session as readable text

Anthropic documents `/export` as the supported readable transcript export, including messages and tool outputs. See [Manage Claude Code sessions](https://code.claude.com/docs/en/sessions).

1. Start Claude Code in the project directory.
2. Run `claude --resume` to open the session picker, or `claude --continue` for the latest session in that directory.
3. Preview and select the correct session.
4. Inside the session, run:

   ```text
   /export /absolute/path/to/ai-data-collection/anthropic/readable-threads/project-session-date.txt
   ```

5. If you run `/export` without a filename, choose copy-to-clipboard or save-to-file from the menu.
6. Confirm the output includes the expected prompts, responses, and tool outputs.

Give important sessions names before collection with `/rename descriptive-name`; this makes selection and later reconciliation easier.

### 4.3 Preserve all local Claude Code transcripts

Claude Code stores local transcripts as plaintext JSONL at:

```text
~/.claude/projects/<project>/<session-id>.jsonl
```

Each line is a message, tool-use, tool-result, or metadata object. Anthropic documents a 30-day default cleanup period, configurable through `cleanupPeriodDays`. Collect promptly.

#### macOS, Linux, and WSL

```sh
CLAUDE_DATA_DIR="${CLAUDE_CONFIG_DIR:-$HOME/.claude}"
mkdir -p "/path/to/ai-data-collection/anthropic/claude-code-local-raw"
cp -a "$CLAUDE_DATA_DIR/projects" "/path/to/ai-data-collection/anthropic/claude-code-local-raw/"
```

#### Windows PowerShell

```powershell
$ClaudeDataDir = if ($env:CLAUDE_CONFIG_DIR) { $env:CLAUDE_CONFIG_DIR } else { Join-Path $env:USERPROFILE ".claude" }
$Destination = "D:\ai-data-collection\anthropic\claude-code-local-raw"
New-Item -ItemType Directory -Force -Path $Destination | Out-Null
Copy-Item (Join-Path $ClaudeDataDir "projects") $Destination -Recurse
```

Do not assume the local files are sanitized. Anthropic describes them as plaintext and they can include code, commands, file paths, and tool output.

For future scripted runs, capture structured output at creation time:

```sh
claude -p --output-format stream-json "your task" > "session-$(date +%Y%m%d-%H%M%S).jsonl"
```

Anthropic also documents a `SessionEnd` hook that receives a `transcript_path`, allowing a future archival workflow without scanning folders.

### 4.4 Collect Claude Code web/cloud sessions

#### Preferred: teleport the session to the CLI, then export

1. Install and sign in to Claude Code with the same Claude account.
2. From a clean working tree, run `claude --teleport`.
3. Select the cloud session, or run `claude --teleport <session-id>`.
4. Claude fetches the branch and loads the full conversation history into a new local copy.
5. Run `/export /absolute/path/to/transcript.txt`.
6. Copy the resulting local JSONL from `~/.claude/projects` as well.

Anthropic notes that the local copy and the claude.ai cloud session stop syncing after teleport. See [Claude Code on the web: web to terminal](https://code.claude.com/docs/en/claude-code-on-the-web).

#### Alternative: resume from the Claude Code VS Code extension

1. Open the Claude Code panel.
2. Select **Session history**.
3. Select the **Web** tab.
4. Choose the cloud session to download and continue locally.
5. Export from the local session.

Only web sessions started with a GitHub repository appear in this VS Code list, and local changes do not sync back to claude.ai. See [Claude Code in VS Code](https://code.claude.com/docs/en/ide-integrations).

### 4.5 Claude Desktop Code tab

Claude Desktop’s Code tab uses the Claude Code engine but maintains session history separately from the CLI. Anthropic does not document a stable private Desktop database as a supported export surface.

For each important Desktop Code session:

1. Open the session and switch the transcript view to the most detailed mode needed.
2. Try `/export` from the built-in command picker if it is available in your installed version.
3. If it is not available, use the session’s **Continue in** handoff to a supported local surface, or copy the visible transcript.
4. Also request the Claude account export and verify whether the session appears there.
5. Keep the readable export and any raw local copy together, labeled with the originating Desktop version and project/worktree.

Do not claim complete Desktop Code coverage until you compare the session list against the files you collected.

### 4.6 Claude Code in VS Code

There are two distinct experiences:

- **Anthropic’s Claude Code panel:** Use its **Session history** button, resume the session, and use `/export` when available. Its CLI-compatible local transcripts are under `~/.claude/projects` on the machine or remote extension host where Claude Code ran.
- **VS Code native Chat/Agents window with Claude selected as the harness:** Use VS Code’s **Chat: Export Chat...** JSON export and **Copy All** Markdown procedure in section 6.

Collect from both if you used both. A history visible in one UI is not proof that the other UI contains it.

### 4.7 Anthropic billing, receipts, and usage

#### Pro or Max purchased directly from Anthropic

1. Open Claude web or Desktop.
2. Go to **Settings → Billing**.
3. Find **Invoices**.
4. Select **View** for each invoice and save it.
5. Search the billing inbox for **“Your receipt from Anthropic”** to recover emailed receipts.

Anthropic documents these steps in [Paid plan billing FAQs](https://support.claude.com/en/articles/8325618-paid-plan-billing-faqs).

#### Claude purchased through iOS or Android

- iOS purchases are handled by Apple; use [Apple purchase history](https://support.apple.com/en-us/118212).
- Android purchases are handled by Google Play; use [Google Play order history](https://support.google.com/googleplay/answer/2850369?hl=en).
- Confirm which platform originally created the subscription; the vendor’s web billing page will not necessarily contain app-store receipts.

#### Claude API/Console and Claude Code API usage

1. Sign in to the [Claude Console](https://platform.claude.com/).
2. Open **Billing** and save the invoice-history or receipt records available to your role.
3. Open the Console **Usage** or Claude Code analytics page and export/save the relevant period.
4. In Claude Code, run `/usage` to record session token usage and plan limits.
5. Treat `/usage` dollar values and OpenTelemetry `claude_code.cost.usage` as estimates. Anthropic says the billing page or provider bill is authoritative; see [Claude Code cost management](https://code.claude.com/docs/en/costs).
6. If Claude Code used Amazon Bedrock, Google Vertex AI, Microsoft Foundry, or another provider, obtain the authoritative bill and usage export from that cloud provider, not Anthropic.

## 5. Cursor IDE and Cursor CLI

### 5.1 Export a local Cursor IDE chat to Markdown

Cursor’s supported preservation format for local foreground chats is Markdown. Current staff guidance calls the menu item **Export Transcript**.

1. Open Cursor and the project.
2. Open Agent/Chat history.
3. Select the conversation.
4. Right-click the chat tab and select **Export Transcript**.
5. If the chat is open as an editor tab, use the `⋯` menu in the editor title bar and select **Export Transcript**.
6. Save the `.md` file under `cursor/readable-threads/` with project, date, and chat title.
7. Open the saved file and verify that user messages, assistant responses, code blocks, and relevant tool output are present.

Cursor documents local history and Markdown preservation in [Cursor chat history](https://docs.cursor.com/en/agent/chat/history). The renamed menu and the Cloud Agent limitation are confirmed in a [Cursor support response](https://forum.cursor.com/t/dump-to-md-file-menu-entry-gone/156366).

There is no supported “export every local chat to Markdown” command. Repeat for all high-value chats, then preserve the raw local store.

### 5.2 Preserve Cursor’s local IDE data

Cursor says regular chat history is stored locally in SQLite. Storage details have changed across releases, so preserve the encompassing data rather than extracting one undocumented key.

#### Close Cursor first

Quit every Cursor window and confirm no Cursor process is still writing. SQLite backups taken while the app is open can be inconsistent.

#### Copy these locations when present

**macOS**

```text
~/Library/Application Support/Cursor/User/globalStorage/
~/Library/Application Support/Cursor/User/workspaceStorage/
~/.cursor/projects/
~/.cursor/chats/
```

**Windows**

```text
%APPDATA%\Cursor\User\globalStorage\
%APPDATA%\Cursor\User\workspaceStorage\
%USERPROFILE%\.cursor\projects\
%USERPROFILE%\.cursor\chats\
```

**Linux**

```text
~/.config/Cursor/User/globalStorage/
~/.config/Cursor/User/workspaceStorage/
~/.cursor/projects/
~/.cursor/chats/
```

Preserve `state.vscdb`, `state.vscdb.backup`, and any matching `-wal`, `-shm`, or `.corrupted.*` files. Staff responses in Cursor’s forum describe transcripts under `~/.cursor/projects`/`~/.cursor/chats` and metadata in SQLite; for example, see [Cursor CLI local chat storage](https://forum.cursor.com/t/cursor-agent-create-chat-and-how-to-delete-old-chats/156640/7) and [Cursor workspace storage paths](https://forum.cursor.com/t/agents-panel-right-sidebar-lost-session-list-no-way-to-restore/153481/4).

These are preservation paths, not stable public schemas. Do not run `DELETE`, `VACUUM`, repair, migration, or third-party extractor tools against the only copy.

If Cursor was launched with `--user-data-dir`, used portable mode, or ran through remote SSH/WSL/dev containers, collect that custom/remote data directory too.

### 5.3 Cursor CLI sessions

Cursor documents these session commands:

```sh
cursor-agent ls
cursor-agent resume
cursor-agent --resume="chat-id-here"
```

See [Using Cursor CLI](https://docs.cursor.com/en/cli/using).

To collect existing sessions:

1. Run `cursor-agent ls` in each relevant environment and save or screenshot the session inventory.
2. Resume each high-value session and copy the visible transcript if no export option is offered.
3. Quit the CLI.
4. Preserve `~/.cursor/chats` and `~/.cursor/projects` from that environment.
5. Note that the CLI prompt-history file is only an input-history list and is not a full conversation transcript.

For future scripted runs, capture structured output when the run happens:

```sh
cursor-agent -p --output-format stream-json "your task" > "cursor-run-$(date +%Y%m%d-%H%M%S).jsonl"
```

The CLI’s structured formats include session identifiers and events; see [Cursor CLI output formats](https://docs.cursor.com/en/cli/reference/output-format).

Cursor CLI and Cursor IDE histories are currently separate. Back up both stores.

### 5.4 Cursor Background/Cloud Agents

Cursor’s regular local history does not contain Background Agent chats; Cursor says those are stored remotely and accessed with `Ctrl+E` or at [cursor.com/agents](https://cursor.com/agents). See [Cursor Background Agents](https://docs.cursor.com/background-agent).

Current Cursor support guidance says Cloud Agent chats cannot use **Export Transcript**. Use this procedure:

1. Open `cursor.com/agents` and inventory every expected agent by title, repository, branch/PR, date, status, and URL.
2. Open each high-value agent and copy the visible conversation into Markdown.
3. Save the shared/read-only link only as a locator, not as the sole archive.
4. Save associated diffs, commits, branches, pull requests, and generated files separately; a transcript does not preserve the repository state.
5. Submit an access-and-portability request to Cursor for the remote personal data it holds.
6. Reconcile Cursor’s response against your web-agent inventory.

Suggested request:

```text
Subject: Personal data access and portability request

Hello Cursor Privacy Team,

I am requesting a copy of the personal data associated with my Cursor account under the access and portability rights described in your Privacy Policy. Please include, where retained and attributable to my account:

- foreground Agent/Chat inputs and outputs;
- Background/Cloud Agent prompts, responses, tool activity, timestamps, titles, repository/session identifiers, and shared-link metadata;
- Cursor CLI interaction/session data held by Cursor;
- account, device, usage, telemetry, support, and billing metadata;
- a data dictionary or field description for the export; and
- the retention period and any categories omitted from the export.

Account email: <email>
Approximate date range: <start> through <end>
Relevant workspaces/teams: <names>

Please provide the data in a portable machine-readable format and explain any identity-verification steps.
```

Cursor’s privacy policy recognizes access and portability rights and directs requests to `hi@cursor.com`; see the [Cursor Privacy Policy](https://cursor.com/privacy).

### 5.5 Cursor billing and invoices

1. Go to [cursor.com/dashboard/billing](https://cursor.com/dashboard/billing).
2. Select **Manage Subscription** to open the Stripe billing portal.
3. Scroll to billing history.
4. Open each invoice and download the PDF.

Cursor says invoices currently require manual download. Team and Enterprise invoices are available only to team admins; after a subscription ends, contact support with the needed date range. See [Cursor invoices](https://prod.cursor.com/help/account-and-billing/invoices).

Also save:

- the Cursor usage page for the relevant billing cycle;
- on-demand or usage-based charge details;
- plan, seat, and workspace information; and
- any app-store receipt if the subscription was purchased through Cursor’s iOS app.

Usage screens explain consumption; the Stripe invoice is the authoritative charge record.

## 6. Visual Studio Code: Copilot, Claude, and Codex sessions

### 6.1 Understand which VS Code UI created the session

Current VS Code has a native **Chat view/Agents window** that can use **Copilot, Claude, or Codex** as the agent harness. It can also discover supported local sessions created by Copilot CLI, the GitHub Copilot app, Claude Code, and Codex. Separately, the first-party Claude Code and Codex extensions can expose their own panels and stores.

Therefore collect both:

1. sessions in VS Code’s native Chat/Agents UI; and
2. vendor-extension sessions from `~/.claude` or `~/.codex`.

VS Code’s cross-surface model is described in [Manage agent sessions](https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions).

### 6.2 Export any native VS Code agent session to JSON

This is the preferred procedure for a Copilot, Claude, or Codex session visible in VS Code’s native Chat view.

1. Open the **Chat** view.
2. Select the session from the sessions list.
3. Open the Command Palette:
   - macOS: `Shift+Command+P`
   - Windows/Linux: `Ctrl+Shift+P`
4. Run **Chat: Export Chat...**.
5. Choose a destination under `vscode-github/exported-sessions/`.
6. Keep the resulting JSON. VS Code says it includes all prompts and responses.
7. Open the file and verify the first and last turns, tool calls, and session title/date.

Official instructions: [VS Code chat-session export](https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions#_export-a-chat-session-as-a-json-file).

### 6.3 Copy a VS Code session as Markdown

1. Open the session.
2. Right-click a message or the chat background.
3. Select **Copy All**.
4. Paste into a `.md` file.

VS Code says **Copy All** includes prompts, responses, thinking steps, and tool calls. **Copy Final Response** saves only the final section and is not a complete transcript.

Keep both JSON and Markdown for important sessions: JSON is structured; Markdown is immediately readable.

### 6.4 GitHub Copilot session sync and local history

Current VS Code documentation says local Copilot agent sessions sync to the user’s GitHub account by default when session sync is enabled. Synced sessions are private unless explicitly shared.

To review collection status:

1. Open the Copilot status area in the Chat view.
2. Check whether session sync says **Enabled**, **N sessions synced**, **Disabled by policy**, or **Sync error**.
3. Select the synced count to view sessions on GitHub.com.
4. Export the important sessions from VS Code even if they are synced.

To keep future sessions local only, set:

```json
"chat.sessionSync.enabled": false
```

To exclude selected repositories:

```json
"chat.sessionSync.excludeRepositories": [
  "owner/private-repo",
  "owner/secret-*"
]
```

Turning sync off does not delete already-synced sessions. To remove them, run **Delete Session Sync Data** from the Command Palette and choose cloud-only or local-and-cloud. Collection should happen before deletion. See [VS Code session history and sync](https://code.visualstudio.com/docs/agents/run/sessions/session-history).

### 6.5 Preserve VS Code’s raw local user data

VS Code says the session-history index uses local SQLite. For recovery-oriented preservation, quit VS Code and copy the whole user-data directory rather than guessing which private database key contains a session.

Default user-data roots:

```text
macOS:  ~/Library/Application Support/Code/
Windows: %APPDATA%\Code\
Linux:  ~/.config/Code/
```

These roots are documented by [VS Code Portable Mode](https://code.visualstudio.com/docs/setup/portable). If you used Insiders, portable mode, or `--user-data-dir`, collect that separate directory too.

The copy can contain extension state and credentials. Encrypt it and do not share it as casually as a per-session JSON export. After restoring files, VS Code documents `/chronicle:reindex` or **Reindex Sessions** to rebuild the local session index.

For Remote SSH, WSL, and dev containers, also collect the remote extension-host data and the vendor session directory on the remote system.

### 6.6 First-party Claude Code extension in VS Code

1. Open the Claude Code panel.
2. Select **Session history**.
3. Inventory the **Local** and **Web** tabs.
4. Resume and export local sessions with `/export` when available.
5. Download eligible web sessions by selecting them from the **Web** tab, then export the local copy.
6. Copy `~/.claude/projects` from the extension host.
7. Request the Claude account export as the account-level layer.

If the same session appears in VS Code’s native Agents window, export that representation with **Chat: Export Chat...** too.

### 6.7 First-party Codex extension in VS Code

1. Open the Codex session history and inventory expected sessions.
2. Export any session adopted into VS Code’s native Chat view using **Chat: Export Chat...**.
3. Quit VS Code.
4. Copy `$CODEX_HOME/sessions`, `archived_sessions`, and `session_index.jsonl` from the extension host.
5. Request the ChatGPT account export for account/cloud data.

Codex local surfaces share the canonical JSONL rollout mechanism. Do not assume a VS Code workspace database alone contains the full Codex transcript.

### 6.8 GitHub account export: useful but not a transcript guarantee

GitHub offers a personal account archive:

1. GitHub profile picture → **Settings**.
2. Select **Account**.
3. Under **Export account data**, select **Start export** or **New export**.
4. Download the emailed `tar.gz`; the link normally expires after seven days.

See [Requesting a GitHub personal-account archive](https://docs.github.com/en/get-started/archiving-your-github-personal-account-and-public-repositories/requesting-an-archive-of-your-personal-accounts-data).

GitHub describes this archive primarily as repository and profile metadata. It does not promise that it is a complete Copilot/VS Code transcript export. Use per-session VS Code JSON exports as the authoritative readable collection.

### 6.9 VS Code and Copilot billing records

VS Code itself is free. Billing follows the provider:

| VS Code feature | Billing provider |
|---|---|
| GitHub Copilot | GitHub |
| Claude Code extension or Claude harness | Anthropic, or the configured cloud provider |
| Codex extension or Codex harness | OpenAI, or the configured provider |

#### GitHub Copilot receipts/invoices

1. On GitHub, open **Settings → Billing & Licensing**.
2. Select **Payment history**.
3. Open each payment.
4. Download the receipt or invoice.

GitHub documents downloadable past payments in [Managing payment and billing information](https://docs.github.com/en/billing/how-tos/set-up-payment/manage-payment-info#viewing-payment-history).

#### GitHub Copilot usage

1. Open [GitHub billing](https://github.com/settings/billing).
2. Select **AI usage**.
3. Save the current cycle’s included credits, additional usage, model breakdown, credits, and costs.
4. Download a usage report where offered.

For an individual Copilot plan, GitHub documents this view in [Monitoring GitHub AI Credits usage](https://docs.github.com/en/copilot/how-tos/manage-and-track-spending/monitor-ai-usage). The GitHub billing API can also return user-level AI-credit usage for directly billed individual plans; it is a usage report, not a receipt.

For organization-managed Copilot, ask the billing manager for the invoice and the organization usage/activity report. A Copilot activity report contains metrics, not private conversation transcripts.

## 7. Reconciliation and completeness checks

Create an inventory with one row per thread/session:

| Field | Example |
|---|---|
| Provider/surface | OpenAI / Codex CLI |
| Account/workspace | personal@example.com / Personal |
| Title | Refactor authentication |
| Session/thread ID | UUID or URL, if available |
| Project/repository | owner/repo |
| Started / last active | ISO timestamps with timezone |
| Exported readable file | `refactor-auth.md` |
| Raw source | `rollout-...jsonl` |
| Account export present | Yes/No/Unknown |
| Attachments/artifacts saved | Yes/No |
| Billing period | 2026-08 |
| Gaps/notes | Cloud chat copied manually |

Then perform these checks:

1. Compare every UI history list against readable exports and raw files.
2. Search account exports for known distinctive prompt text, not only titles.
3. Compare first and last message timestamps in readable and raw versions.
4. Confirm archived sessions were included.
5. Confirm WSL, SSH, remote-container, VM, and secondary-device stores were collected.
6. Confirm attachments, generated files, branches, commits, and pull requests were saved separately.
7. Compare billing invoices/receipts with payment-card or app-store history.
8. Compare invoice totals with provider usage/cost exports for the same account, period, currency, and UTC boundary.
9. Record every unresolved omission instead of silently treating it as absent data.

## 8. Optional integrity manifest

After collection, calculate hashes so later changes are detectable.

### macOS/Linux

Run from the collection root:

```sh
find . -type f ! -name SHA256SUMS.txt -print0 | sort -z | xargs -0 shasum -a 256 > SHA256SUMS.txt
```

### Windows PowerShell

```powershell
Get-ChildItem "D:\ai-data-collection" -File -Recurse |
  Get-FileHash -Algorithm SHA256 |
  Export-Csv "D:\ai-data-collection\SHA256SUMS.csv" -NoTypeInformation
```

Record the date, timezone, device, collector, account/workspace, and commands used in `00-collection-notes/README.md`.

## 9. Recommended recurring routine

For an active individual user:

- **After a critical agent task:** export that thread to Markdown/text/JSON and save repository artifacts.
- **Weekly:** back up Codex, Claude Code, Cursor, and VS Code local stores from every active environment.
- **Monthly:** request or download account exports when the vendor permits; collect invoices and usage CSVs after the billing period closes.
- **Before changing plans, workspaces, accounts, machines, IDE profiles, or major versions:** perform a full collection and verify it before migration.
- **Before deleting any chat or clearing old history:** export and hash it first.

## 10. Key limitations to remember

1. **Deletion is usually final.** An export cannot recreate a chat already deleted from the service and local stores.
2. **Cloud and local are different evidence sources.** A local database does not necessarily contain cloud-agent sessions.
3. **Readable exports can omit implementation detail.** Preserve raw JSONL/SQLite copies for tool events, metadata, and recovery.
4. **Raw copies can expose credentials and private code.** Encrypt and tightly restrict them.
5. **Usage is not an invoice.** Collect both when reconciling spend.
6. **Provider matters.** Claude or Codex used through Bedrock, Vertex, Foundry, or another gateway is billed and logged partly by that provider.
7. **VS Code is a host, not the biller.** Copilot, Claude, and Codex billing must be collected from GitHub, Anthropic, and OpenAI respectively.
8. **Vendor schemas and menus change.** Keep the original export, record versions, and prefer supported UI exports over database reverse-engineering.

## 11. Primary references

### OpenAI

- [Exporting ChatGPT history and data](https://help.openai.com/en/articles/7260999-how-do-i-export-my-chatgpt-history-and-data)
- [Exporting data from the ChatGPT Android app](https://help.openai.com/en/articles/8167885-how-to-export-your-data-from-the-chatgpt-android-app)
- [ChatGPT iOS app FAQ](https://help.openai.com/en/articles/7885016-chatgpt-ios-app-faq)
- [ChatGPT macOS app data retention](https://help.openai.com/en/articles/9268871)
- [Finding past ChatGPT invoices](https://help.openai.com/en/articles/12356340-how-can-i-find-my-past-chatgpt-invoices/)
- [Codex chat archiving and deletion](https://help.openai.com/en/articles/20001333-how-to-archive-and-delete-codex-chats-in-the-chatgpt-app)
- [Using Codex with a ChatGPT plan](https://help.openai.com/en/articles/11369540-using-codex-with-your-chatgpt-plan)
- [Codex rollout recorder source](https://github.com/openai/codex/blob/main/codex-rs/rollout/src/recorder.rs)
- [Codex message-history source](https://github.com/openai/codex/blob/main/codex-rs/message-history/src/lib.rs)
- [Using ChatGPT credits](https://help.openai.com/en/articles/12642688-using-credits-for-flexible-usage-in-chatgpt-free-go-plus-pro-sora)
- [OpenAI API invoice and payment-history rules](https://help.openai.com/en/articles/6640792-when-can-i-expect-to-receive-my-openai-api-invoice)
- [Exporting OpenAI API usage and cost data](https://help.openai.com/en/articles/20001072)

### Anthropic

- [Export your Claude data](https://support.claude.com/en/articles/9450526-export-your-claude-data)
- [Manage Claude Code sessions and exports](https://code.claude.com/docs/en/sessions)
- [Claude Code in VS Code](https://code.claude.com/docs/en/ide-integrations)
- [Claude Code on the web](https://code.claude.com/docs/en/claude-code-on-the-web)
- [Claude Code data usage](https://code.claude.com/docs/en/data-usage)
- [Claude Code cost management](https://code.claude.com/docs/en/costs)
- [Paid plan billing FAQs](https://support.claude.com/en/articles/8325618-paid-plan-billing-faqs)

### Cursor

- [Cursor chat history](https://docs.cursor.com/en/agent/chat/history)
- [Cursor CLI usage](https://docs.cursor.com/en/cli/using)
- [Cursor CLI output formats](https://docs.cursor.com/en/cli/reference/output-format)
- [Cursor Background Agents](https://docs.cursor.com/background-agent)
- [Cursor Privacy Policy](https://cursor.com/privacy)
- [Cursor invoices](https://prod.cursor.com/help/account-and-billing/invoices)
- [Support clarification: Export Transcript and Cloud Agent limitation](https://forum.cursor.com/t/dump-to-md-file-menu-entry-gone/156366)
- [Support clarification: Cursor CLI local-chat storage](https://forum.cursor.com/t/cursor-agent-create-chat-and-how-to-delete-old-chats/156640/7)
- [Support clarification: Cursor workspace storage paths](https://forum.cursor.com/t/agents-panel-right-sidebar-lost-session-list-no-way-to-restore/153481/4)

### Visual Studio Code and GitHub

- [Manage and export VS Code agent sessions](https://code.visualstudio.com/docs/agents/run/sessions/manage-sessions)
- [VS Code session history, sync, and privacy controls](https://code.visualstudio.com/docs/agents/run/sessions/session-history)
- [VS Code Portable Mode and user-data roots](https://code.visualstudio.com/docs/setup/portable)
- [Request a GitHub personal-account archive](https://docs.github.com/en/get-started/archiving-your-github-personal-account-and-public-repositories/requesting-an-archive-of-your-personal-accounts-data)
- [GitHub payment history and downloadable receipts](https://docs.github.com/en/billing/how-tos/set-up-payment/manage-payment-info)
- [Monitor GitHub Copilot AI-credit usage](https://docs.github.com/en/copilot/how-tos/manage-and-track-spending/monitor-ai-usage)