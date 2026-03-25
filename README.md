# Dating CRM

Automated dating pipeline built on [Claude Code](https://claude.com/claude-code). Manages Tinder and WhatsApp conversations through MCP servers and recurring cron jobs.

## How it works

Claude Code slash commands run on cron schedules to:
- Check Tinder for new messages and reply
- Send openers to new Tinder matches
- Move conversations to WhatsApp when numbers are shared
- Follow up on stale conversations
- Track contacts, conversation history, and date scheduling

## Components

### Slash Commands (`commands/`)

| Command | Frequency | What it does |
|---------|-----------|--------------|
| `/start-crm` | Once per session | Spins up all cron jobs |
| `/tinder-frequent` | Every 30 min | Replies to unread Tinder messages |
| `/tinder-daily` | Daily | Openers, stale convo revival, analytics |
| `/whatsapp-frequent` | Every 15 min | Replies to unread WhatsApp messages |
| `/whatsapp-daily` | Daily | Follow-ups, cross-platform sync, date logistics |

### WhatsApp MCP (`whatsapp-mcp/`)

Two-part MCP server for WhatsApp integration:
- **whatsapp-bridge** — Go service using [whatsmeow](https://github.com/tulir/whatsmeow) that connects to WhatsApp Web and stores messages in SQLite
- **whatsapp-mcp-server** — Python MCP server exposing tools for searching contacts, reading/sending messages, and media

### Tinder MCP (separate repo)

The Tinder MCP server lives in its own repo: [tinder-mcp-server](https://github.com/YehudaGoldshtein/tinder-mcp-server)

### Data (gitignored)

These files are created locally and not committed:
- `data/contacts.json` — Contact database with profile info, conversation state, and status
- `data/conversations/{name}.md` — Per-contact conversation logs bridging Tinder and WhatsApp context
- `data/schedule.json` — Date scheduling with booked time slots
- `dating-texting-personal.md` — Personal rules and availability

## Setup

### Prerequisites

- [Claude Code](https://claude.com/claude-code) CLI
- Go 1.21+ (for WhatsApp bridge)
- Python 3.11+ and [uv](https://github.com/astral-sh/uv) (for WhatsApp MCP server)
- Tinder MCP server installed separately

### Steps

1. **Clone and build WhatsApp bridge:**
   ```bash
   cd whatsapp-mcp/whatsapp-bridge
   go build -o whatsapp-bridge.exe .
   ./whatsapp-bridge.exe  # scan QR code to pair
   ```

2. **Register MCP servers** in `~/.claude/settings.json`:
   ```json
   {
     "mcpServers": {
       "whatsapp": {
         "command": "uv",
         "args": ["--directory", "<path>/whatsapp-mcp/whatsapp-mcp-server", "run", "main.py"]
       }
     }
   }
   ```

3. **Install slash commands** — copy or symlink `commands/*.md` into `~/.claude/commands/`

4. **Create data files:**
   ```bash
   mkdir -p data/conversations
   echo '{"contacts":[],"analytics":{},"last_tinder_check":null,"last_whatsapp_check":null}' > data/contacts.json
   echo '{"booked":[]}' > data/schedule.json
   ```

5. **Start a session:**
   ```
   /start-crm
   ```

## Note

The slash commands currently reference hardcoded Windows paths (`C:/Users/yehud/...`). Update these to match your environment.
