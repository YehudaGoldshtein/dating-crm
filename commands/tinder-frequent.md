---
name: tinder-frequent
description: Check Tinder messages and reply to active conversations (every 30 min)
---

## Tinder Frequent Check (every 30 min)

You are running an automated Tinder message check. Follow these steps carefully.

### CRITICAL RULE: NEVER message Israeli contacts on WhatsApp. If a phone number starts with +972, or the person uses Hebrew, or has any Israeli indicators — skip them entirely. No exceptions.

### RATE LIMIT: Max 5 messages total this session. Count every send_message call.

### Steps:

1. **Check Tinder status** — use `tinder_status`. If not logged in, use `tinder_login` and wait.

2. **Load contacts DB** — read `C:/Users/yehud/Documents/dating-crm/contacts.json`

3. **Get recent conversations** — use `tinder_conversations_since` with the `last_tinder_check` value from the DB. All timestamps MUST be UTC with `Z` suffix (e.g. `"2026-03-25T14:30:00Z"`). If null, use 2 days ago. To get current UTC time, use `new Date().toISOString()`.

4. **Identify conversations needing response** — find convos where the last message is FROM the match (i.e., they messaged and we haven't replied). Skip convos where `tinder_status` is `dead` or `moved_to_whatsapp`, or where `blacklisted` is `true`.

5. **For each conversation needing response (max 5):**
   a. Check if contact already exists in DB with full profile info. If YES: read their conversation log at `conversations/{name}.md` for context instead of scanning. Only use `tinder_scan_profile` if the contact is NOT in the DB or has missing/null profile fields.
   b. If scan was needed, create the contact in DB with all profile info
   c. Craft a reply following the dating texting guide at `C:/Users/yehud/Documents/dating-texting-guide.md`
   d. **Style rules:**
      - Check `is_trans` field or scan bio for trans indicators. If trans: use submissive/worshipful energy
      - Otherwise: match their sexual/flirty energy, don't play it safe
      - Keep messages short, playful, move toward getting their number or a date
   e. Use `tinder_send_message` to send the reply
   f. Update the contact in the DB
   g. **Update conversation log** — write/append to `C:/Users/yehud/Documents/dating-crm/conversations/{name}.md` with a summary of the exchange: key topics, vibe, anything they shared about themselves, and your reply. This file is critical — WhatsApp routines read it to know the Tinder context.

6. **If a match shares their phone number** — extract it, update `whatsapp_phone` in DB, set `tinder_status` to `moved_to_whatsapp`, then **send an opening WhatsApp message** using `send_message`. Reference the Tinder convo so it feels seamless (e.g. "hey it's Yehuda from Tinder 😏" + something specific from the chat). Read the conversation log at `C:/Users/yehud/Documents/dating-crm/conversations/{name}.md` for context. Log the WhatsApp opener there too.

7. **Update DB** — set `last_tinder_check` to now as UTC with Z suffix (use `node -e "console.log(new Date().toISOString())"`), save contacts.json

8. **Report** — brief summary of what you did: who you replied to, any numbers collected, any issues.
