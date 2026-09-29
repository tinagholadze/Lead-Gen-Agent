# Lead Gen Agent

## What this project is

A recurring, unattended lead-generation system run by two Claude agents on a schedule.
Every run starts fresh, so everything an agent needs to know is written in this folder.

- **Research agent** finds new potential clients every weekday and adds them to Notion.
- **Communication agent** emails people we have met, watches for replies, and drafts answers for approval.
- **Evening summary** reports the day's activity by email.

## Goals

1. Add 3–10 well-matched new leads to the pipeline every weekday.
2. Send a personal follow-up to every lead marked **Met** within one hour.
3. Never miss a reply: every reply gets a drafted answer and a status update within one hour.
4. Keep Notion as the single source of truth, so nobody has to check Apollo or Gmail by hand.

## Who we are looking for (ICP)

- **Titles:** COO, VP Operations, Head of Operations, Director of Operations
- **Company size:** 50–500 employees
- **Location:** United States (company HQ and person)
- **Industries:** professional services, logistics & supply chain, healthcare administration, real estate
- **Why they buy:** they run operations teams with lots of manual, repetitive work (emails, scheduling, invoicing, reporting) that AI automation can reduce.

## Sender identity (demo brand)

- **Name:** {{YOUR_NAME}}
- **Business:** {{YOUR_BUSINESS}}
- **Offer:** helping operations teams automate repetitive work with AI agents
- **Postal address:** {{YOUR_POSTAL_ADDRESS}}
- **Sending account:** the connected Gmail account

## Connected systems

| System | Used for | Notes |
|---|---|---|
| Apollo.io | Finding leads, revealing emails | Free plan. Use `apollo_agent_find_prospects` for search (direct people search is blocked on this plan). Email reveal costs credits. |
| Notion | Lead database | Database "Lead Pipeline", data source `{{NOTION_DATA_SOURCE_ID}}`. To read rows, ALWAYS use query_data_sources in **view mode** with view_url `{{NOTION_VIEW_URL}}` (Pipeline board). SQL/rows mode hits the Notion free-plan usage limit. |
| Gmail | Sending, reading replies, drafts, summary | Send only as described in `agents/communication-agent.md` |

## Demo mode (ON)

This project runs with synthetic contacts, so no real prospect is ever emailed.
- Every new lead the research agent adds gets **Email = {{YOUR_TEST_INBOX}}** (the demo inbox) and **Source = Apollo**.
- Every new lead gets **Where we met** = one of these events (rotate, don't repeat the same one twice in a row):
  AI Ops Summit, Austin · Logistics Tech Expo, Chicago · HealthOps Forum, Boston · PropTech Connect, New York · Future of Work Conference, San Francisco · SMB Automation Meetup, Denver
- **Notes** = one short line the follow-up can use, based on the lead's title and industry (e.g. "Talked about automating shipment status emails.").
- Because all demo leads share one email address, the communication agent matches emails and replies **by Gmail thread**, never by address alone.
- These demo values are allowed exceptions to the rule "Never invent data".

## Lead statuses

| Status | Meaning | Set by |
|---|---|---|
| New | Found by the research agent, not contacted | Research agent |
| Met | We met this person; OK to send the follow-up | **You (manually)** |
| Sent | Follow-up email sent | Communication agent |
| Awaiting confirmation | Lead replied; a draft answer is waiting for your approval in Gmail | Communication agent |
| In conversation | You sent the approved reply | Communication agent (detects it) |
| Meeting booked | A call is scheduled | You |
| Not interested | Lead declined | Communication agent or you |
| Unsubscribed | Lead asked not to be emailed. Never email again. | Communication agent |

## Files in this project

- `agents/research-agent.md`: daily lead search and the evening summary
- `agents/communication-agent.md`: follow-up emails, reply handling
- `templates/follow-up-email.md`: the approved email template
- `compliance/us-email-rules.md`: rules every email must pass
- `logs/`: one file per day with what each run did

## Rules for every unattended run

1. **Nobody is watching.** Do not ask questions or wait for approval. Make the most reasonable choice, write the reason in the log, and continue.
2. **Any time is a valid time.** Runs are started by the schedule or manually with "Run now", on any day and at any hour. Always run the full workflow. Never skip or comment on a run because it is outside the usual schedule. "Today" always means today's date in {{YOUR_TIMEZONE}}.
3. **Check before acting.** Always read the current Notion status (and Gmail Sent) before doing anything, so a rerun never sends or adds twice.
4. **Email only people with status Met** (first email) or answer people who replied. Never cold-email a New lead.
5. **Never email anyone marked Unsubscribed or Not interested.**
6. **Every email must pass `compliance/us-email-rules.md`.** If it can't, skip it and log why.
7. **Never invent data.** If a field is unknown, leave it empty. (Exception: the demo values in "Demo mode" above.)
8. **Stay within limits:** at most 3 Apollo searches and 10 email reveals per day (manual test runs count too); at most 10 emails sent per run.
9. **If a tool fails,** retry once. If it fails again, log the error and move on to the next item.
10. **Log every run** by appending to `logs/YYYY-MM-DD.md` with counts for each phase.
