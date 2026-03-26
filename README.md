# Dating CRM

Automated dating pipeline built on [Claude Code](https://claude.com/claude-code). Manages Tinder and WhatsApp conversations through MCP servers and recurring cron jobs.

## How it works

Claude Code slash commands run on cron schedules to:
- Check Tinder for new messages and reply
- Send openers to new Tinder matches
- Move conversations to WhatsApp when numbers are shared
- Follow up on stale conversations
- Track contacts, conversation history, and date scheduling

## Repo Structure

```
dating-crm/
├── commands/              # Claude Code slash commands
├── tinder-mcp/            # submodule → Tinder MCP (Playwright automation)
├── whatsapp-mcp/          # submodule → WhatsApp MCP (whatsmeow bridge)
├── data/                  # gitignored: contacts, conversations, schedule
├── dating-texting-guide.md
├── dating-texting-personal.md  # gitignored: personal rules
└── .gitmodules
```

## Components

### Slash Commands (`commands/`)

| Command | Frequency | What it does |
|---------|-----------|--------------|
| `/start-crm` | Once per session | Spins up all cron jobs |
| `/tinder-frequent` | Every 30 min | Replies to unread Tinder messages |
| `/tinder-daily` | Daily | Openers, stale convo revival, analytics |
| `/whatsapp-frequent` | Every 15 min | Replies to unread WhatsApp messages |
| `/whatsapp-daily` | Daily | Follow-ups, cross-platform sync, date logistics |

### Tinder MCP (`tinder-mcp/`)

Git submodule → [YehudaGoldshtein/tinder-automation](https://github.com/YehudaGoldshtein/tinder-automation)

Playwright-based Tinder web automation with 16 MCP tools. Features:
- Swipe automation, match management, message sending
- Full profile scanning (photos, bio, interests, messages)
- Optional `timeout` param on every tool (no more hanging calls)
- `matchId` bypass to skip slow match list scrolling (~90s → ~2s)
- 3-minute match cache
- Detailed logging to `logs/tinder-auto.log`

### WhatsApp MCP (`whatsapp-mcp/`)

Git submodule → [YehudaGoldshtein/whatsapp-mcp](https://github.com/YehudaGoldshtein/whatsapp-mcp) (fork of [lharries/whatsapp-mcp](https://github.com/lharries/whatsapp-mcp))

Two-part MCP server for WhatsApp:
- **whatsapp-bridge** — Go service using [whatsmeow](https://github.com/tulir/whatsmeow) that connects to WhatsApp Web and stores messages in SQLite
- **whatsapp-mcp-server** — Python MCP server for searching contacts, reading/sending messages, and media

Custom improvements over upstream:
- Seamless JID format auto-resolution (`@s.whatsapp.net` ↔ `@lid`) — fixes missed messages when WhatsApp migrates contacts between formats

### Data (gitignored)

Created locally, not committed:
- `data/contacts.json` — Contact database with profile info, conversation state, and status
- `data/conversations/{name}.md` — Per-contact conversation logs bridging Tinder ↔ WhatsApp context
- `data/schedule.json` — Date scheduling with booked time slots
- `dating-texting-personal.md` — Personal rules and availability

## Setup

### Prerequisites

- [Claude Code](https://claude.com/claude-code) CLI
- Node.js 18+ (for Tinder MCP)
- Go 1.21+ (for WhatsApp bridge)
- Python 3.11+ and [uv](https://github.com/astral-sh/uv) (for WhatsApp MCP server)
- Playwright Chromium (`npx playwright install chromium`)

### 1. Clone

```bash
git clone --recurse-submodules https://github.com/YehudaGoldshtein/dating-crm.git
cd dating-crm
```

### 2. Build Tinder MCP

```bash
cd tinder-mcp
npm install
npx playwright install chromium
npm run build
```

First login (opens browser for manual Tinder login):
```bash
npx ts-node src/index.ts login
```

### 3. Build WhatsApp MCP

```bash
cd whatsapp-mcp/whatsapp-bridge
go run main.go  # scan QR code with WhatsApp to pair
```

### 4. Register MCP servers

Add to `~/.claude/settings.json`:

```json
{
  "mcpServers": {
    "tinder": {
      "type": "stdio",
      "command": "node",
      "args": ["<path>/dating-crm/tinder-mcp/dist/mcp-server.js"],
      "cwd": "<path>/dating-crm/tinder-mcp"
    },
    "whatsapp": {
      "command": "uv",
      "args": ["--directory", "<path>/dating-crm/whatsapp-mcp/whatsapp-mcp-server", "run", "main.py"]
    }
  }
}
```

### 5. Install slash commands

Copy or symlink into Claude Code commands directory:

```bash
cp commands/*.md ~/.claude/commands/
```

### 6. Create data files

```bash
mkdir -p data/conversations
echo '{"contacts":[],"analytics":{},"last_tinder_check":null,"last_whatsapp_check":null}' > data/contacts.json
echo '{"booked":[]}' > data/schedule.json
```

### 7. Start

```
/start-crm
```

## Updating submodules

To pull latest changes from both MCP repos:

```bash
git submodule update --remote
```

## Note

The slash commands currently reference hardcoded Windows paths (`C:/Users/yehud/...`). Update these to match your environment.
