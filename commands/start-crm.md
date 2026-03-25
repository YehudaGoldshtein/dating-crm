---
name: start-crm
description: Spin up all dating CRM cron jobs for this session
---

## Start Dating CRM Crons

Set up all recurring cron jobs for the dating CRM system. Run this once at the start of each session.

### Startup sequence:

1. **Immediately run `/tinder-frequent`** — execute it right now via the Skill tool, don't wait for cron.
2. **After Tinder finishes, wait 10 minutes, then run `/whatsapp-frequent`** — schedule a one-shot cron 10 min from now with `recurring: false`.
3. Then set up the recurring crons below.

### Create these recurring cron jobs:

1. **Tinder frequent** (every 30 min) — schedule at :23 and :53 each hour
   - Prompt: `/tinder-frequent`

2. **WhatsApp frequent** (every 15 min) — schedule at :07, :22, :37, :52 each hour
   - Prompt: `/whatsapp-frequent`

3. **Tinder daily** — schedule at 10:03 AM
   - Prompt: `/tinder-daily`

4. **WhatsApp daily** — schedule at 10:17 AM
   - Prompt: `/whatsapp-daily`

Use `CronCreate` for each one. All recurring. Then confirm with `CronList` and report what's running.

Note: These auto-expire after 7 days.
