# Research Agent

**Schedule:** weekdays 08:00 ({{YOUR_TIMEZONE}})
**Job:** find new leads that match the ICP in `CLAUDE.md` and add the good ones to Notion.
**Also runs:** the evening summary at 18:30 (Phase 6).

Read `CLAUDE.md` first. Follow the phases in order.

---

## Phase 1: Pick today's focus

Rotate the industry by weekday so each search returns different people:

| Day | Industry |
|---|---|
| Monday | professional services |
| Tuesday | logistics & supply chain |
| Wednesday | healthcare administration |
| Thursday | real estate |
| Friday | all four industries |
| Saturday, Sunday (manual runs) | all four industries |

**Output:** today's industry.

## Phase 2: Search Apollo

Call `apollo_agent_find_prospects` with this instruction (fill in the industry):

> COO / VP Operations / Head of Operations at US companies with 50–500 employees in {industry}.

The task runs in the background: poll it with the returned `task_id` every ~15 seconds until it finishes.
Take up to 10 people from the result (name, title, company, LinkedIn URL, Apollo person URL).

**Output:** up to 10 candidates. Log the count.

## Phase 3: Remove duplicates

Query the Notion data source for existing leads. A candidate is a duplicate if any of these match an existing row:
the Apollo ID (the 24-character id in the Apollo person URL), the LinkedIn URL, or name + company.

If every candidate is a duplicate, continue the same Apollo task once and ask for more results. Then repeat this phase.

**Output:** new candidates only. Log the count.

## Phase 4: Score fit (1–100)

Score each candidate with this rubric and write one sentence explaining the score:

| Signal | Points |
|---|---|
| Title is clearly an operations leader (COO, VP/Head/Director of Operations) | up to 30 |
| Company is 50–500 employees | up to 20 |
| Industry is one of the four target industries | up to 25 |
| Signs of manual, repetitive operations work (dispatch, scheduling, claims, billing, property management) | up to 25 |

Keep candidates scoring **60 or higher**. Log the others with their reason and skip them.

**Output:** qualified leads. Log the count.

## Phase 5: Add to Notion

Create one row per qualified lead:

| Property | Value |
|---|---|
| Name | full name |
| Title | job title |
| Company | company name |
| LinkedIn | LinkedIn URL |
| Apollo ID | id from the Apollo person URL |
| Industry | today's industry |
| Location | if known |
| Country | US |
| Status | New |
| Source | Apollo |
| Fit score | score |
| Fit reason | one sentence |
| Added | today's date |
| Email | {{YOUR_TEST_INBOX}} (demo mode, see CLAUDE.md) |
| Where we met | next event from the demo list in CLAUDE.md |
| Notes | one short line based on title and industry |

Do **not** reveal emails with Apollo here. In demo mode every lead already gets the demo inbox address, so no Apollo credits are spent on emails.

**Output:** rows added. Append to `logs/YYYY-MM-DD.md`:

```
## Research run 08:00
Industry: logistics & supply chain
Found 10 → 7 new → 5 qualified → 5 added
Skipped: <name> (score 45: title is Head of Sales, not operations)
```

---

## Phase 6: Evening summary (evening summary run only)

0. **No duplicates:** if today's log already has "## Evening summary sent" from the last 30 minutes, do nothing and stop.
1. Read today's log and query Notion for today's changes.
2. Send one email to the connected Gmail account (to yourself):

**Subject:** Lead Gen Agent: daily summary {date}

**Body (short, plain text):**
- New leads added today: count + names with fit scores
- Follow-ups sent: count + names
- Replies received: count + names
- **Drafts waiting for your approval:** names + Gmail draft links
- Unsubscribes: count
- Apollo credits used today
- Any errors from the log

No other text. If nothing happened today, say so in one line.
