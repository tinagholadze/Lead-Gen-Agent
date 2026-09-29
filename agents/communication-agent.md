# Communication Agent

**Schedule:** every hour, 09:00–19:00 ({{YOUR_TIMEZONE}})
**Job:** send follow-ups to people we met, catch replies, and draft answers for approval.

Read `CLAUDE.md`, `templates/follow-up-email.md` and `compliance/us-email-rules.md` first. Follow the phases in order.

---

## Phase 1: Send follow-ups to Met leads

1. Query Notion for rows with **Status = Met**. Process at most 10.
2. For each lead:
   1. **Email (demo mode):** never call Apollo to reveal emails (the Free plan blocks it). If Email is empty, set it to {{YOUR_TEST_INBOX}}. If Where we met is empty, pick the next event from the demo list in CLAUDE.md. If Notes is empty, write one short line from the title and industry. Save these to Notion, then continue.
   2. **Double-send check:** if the row already has a Thread link or Sent on date, skip it. Otherwise search Gmail Sent for this exact subject (`in:sent subject:"Great meeting you at <where we met>, <first name>"`). If found, set Status = Sent with its date and thread link, and skip. (Several demo leads share one address, so never skip just because the address was emailed before.)
   3. **Write the email** from the template. Write the personal lines from "Where we met" and "Notes". Never invent details.
   4. **Compliance check:** the email must pass every rule in `compliance/us-email-rules.md`. If not, skip and log why.
   5. **Send** with Gmail.
   6. **Update Notion:** Status = Sent, Sent on = today, Last contacted = today, Thread link = Gmail thread URL.

**Output:** log "Met: X → sent: Y → skipped: Z (reasons)".

## Phase 2: Check for replies

1. Query Notion for rows with **Status = Sent** or **In conversation**.
2. For each of them, open its Gmail thread from the **Thread link** (get_thread with the thread id).
3. If the thread has a message from the lead that is newer than our last message, treat it as a reply. Match replies **only by thread**, never by sender address (demo leads share one address).

## Phase 3: Handle each reply

Decide what the reply means, then act:

| Reply means | Status | Action |
|---|---|---|
| Asks to stop or unsubscribe | Unsubscribed | No draft. Never email again. |
| Clearly not interested | Not interested | Draft a short, polite thank-you (for approval). |
| Interested, has a question, or proposes a time | Awaiting confirmation | Draft a helpful reply. |
| Out-of-office or auto-reply | no change | Ignore. |

**Drafting rules:**
- Create the draft as a reply in the same Gmail thread (never send it).
- Answer what they asked, in 3–6 sentences. Suggest one clear next step (e.g. a 20-minute call and two time options).
- Do not promise prices, dates, or results that are not in `CLAUDE.md`.
- Include the footer from the template.

**Update Notion:** Status, Replied on = today, Reply snippet = first 200 characters, Draft link = Gmail draft URL.

## Phase 4: Detect approved replies

For rows with **Status = Awaiting confirmation**: if the draft no longer exists and the thread now contains our sent reply, set Status = **In conversation** and Last contacted = today.

## Phase 5: Log

Append to `logs/YYYY-MM-DD.md`:

```
## Communication run 14:00
Met: 1 → sent: 1
Replies: 1 → drafted: 1, unsubscribed: 0
Approved since last run: 0
Errors: none
```
