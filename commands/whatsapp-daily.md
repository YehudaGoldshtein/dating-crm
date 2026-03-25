---
name: whatsapp-daily
description: Daily WhatsApp review — follow-ups, cross-platform sync, analytics
---

## WhatsApp Daily Routine

You are running the daily WhatsApp dating review. Comprehensive catch-up and analytics.

### Steps:

1. **Load contacts DB** — read `C:/Users/yehud/Documents/dating-crm/contacts.json`

2. **Review ALL dating WhatsApp conversations:**
   - **Skip contacts where `blacklisted` is `true`** — no communication whatsoever
   - For each contact with `whatsapp_phone`, use `list_messages` to get recent history
   - Categorize: active / stale / dead / date planned

3. **Follow up on stale conversations:**
   - Convos where we're waiting on them for 1-2 days: send a light re-engagement
   - Convos where they're waiting on us: respond ASAP
   - Dead convos (5+ days silence): consider a hail-mary if they seemed interested before

4. **Cross-platform sync with Tinder:**
   - Check if any Tinder contacts shared numbers recently (scan contacts DB)
   - For newly moved contacts: read their conversation log at `C:/Users/yehud/Documents/dating-crm/conversations/{name}.md` for full Tinder context, then send a WhatsApp intro that references the Tinder convo
     Example: "Hey [name]! It's Yehuda from Tinder 😊 [reference something specific from tinder convo]"
   - Update both `tinder_status` and `whatsapp_status`

5. **Date logistics:**
   - If any convo is at the "planning to meet" stage, help push toward a concrete plan
   - **Check schedule** — read `C:/Users/yehud/Documents/dating-crm/schedule.json` for availability and already-booked slots
   - Suggest the next open slot (Mon–Thu: 10–12 or 19+, Fri–Sun: all day). Don't double-book.
   - Once a date is confirmed, add it to `schedule.json` `booked` array: `{"name", "date", "time", "location"}`
   - Update `whatsapp_status` to `date_planned` with notes

6. **Analytics:**
   - Active WhatsApp convos count
   - Messages sent/received today
   - Avg response time (theirs)
   - Convos that went from Tinder → WhatsApp this week
   - Dates planned this week
   - What message styles got fastest/most engaged responses

7. **Update contacts DB:**
   - All statuses current
   - `waiting_on` fields updated
   - Mark truly dead convos

8. **Full report:**
   - Dashboard of all active girls: name, platform, status, waiting_on, last interaction
   - Highlights: hottest convos, dates coming up, numbers to follow up
   - What's working vs not (opener styles, message length, topics)
   - Recommendations for tomorrow

9. **Save DB**, update `last_daily_run` and `last_whatsapp_check`.
