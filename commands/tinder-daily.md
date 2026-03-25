---
name: tinder-daily
description: Daily Tinder catch-up — openers, stale convos, analytics, contact sync
---

## Tinder Daily Routine

You are running the daily Tinder review. This is a comprehensive catch-up, not a quick check.

### RATE LIMIT: Max 5-6 messages total. Prioritize highest-value conversations.

### Steps:

1. **Check Tinder status** — use `tinder_status`. Login if needed.

2. **Load contacts DB** — read `C:/Users/yehud/Documents/dating-crm/contacts.json`

3. **Get ALL conversations** — use `tinder_conversations_since` with a date ~7 days ago to get full picture.

4. **Send openers to new matches** — use `tinder_send_openers` to auto-message uncontacted matches. This counts toward rate limit.

5. **Skip all contacts where `blacklisted` is `true`** — no messages, no openers, no analytics. Treat them as invisible.

6. **Identify stale/missed conversations:**
   - Convos where match replied but we didn't follow up (waiting_on = me)
   - Convos that went cold (no messages in 2+ days from either side, but had good energy)
   - Prioritize: girls who sent longer/more engaged messages first

6. **For top priority stale convos (remaining message budget):**
   a. `tinder_scan_profile` for full context
   b. Craft a re-engagement message — reference something specific from their profile or prior convo
   c. Style: match energy, flirty, keep it light. Trans girls get worshipful style.
   d. Send and update DB
   e. **Update conversation log** — write/append to `C:/Users/yehud/Documents/dating-crm/conversations/{name}.md` summarizing the full Tinder convo: topics, vibe, flirty moments, what they shared, interests. WhatsApp routines depend on this file.

7. **Update ALL contacts in DB:**
   - Sync any new matches not yet in contacts
   - Update `waiting_on`, `last_tinder_msg`, `tinder_status` for all active convos
   - Mark convos with no activity in 14+ days as `dead`

8. **Analytics update:**
   - Count openers sent today
   - Count replies received since last daily
   - Response rate = replies / openers
   - Update analytics in contacts.json

9. **Cross-platform sync:**
   - For contacts with `whatsapp_phone` set, check if WhatsApp convo exists
   - Update status accordingly

10. **Report:**
    - New matches today
    - Messages sent / replies received
    - Response rate
    - Hottest active convos (most engaged)
    - Stale convos revived
    - Numbers collected
    - What opener styles worked best (if enough data)

11. **Update** `last_daily_run` in DB, save.
