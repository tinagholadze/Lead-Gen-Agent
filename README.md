# Lead Gen Agent

Agentic AI system built with Claude: a research agent finds and scores leads in Apollo, and a communication agent follows up, catches replies and drafts answers for your approval. Tools: Notion, Gmail, Apollo.io.

Built for a live workshop on Claude use cases for the Women AI Builders group.

---

## What it does

| Agent | When | What it does |
|---|---|---|
| **Research agent** | Weekday mornings | Searches Apollo for your ideal clients, removes duplicates, scores fit (1–100 with a reason) and adds the good ones to Notion as **New**. |
| **You** | After you meet someone | Change their status to **Met** in Notion. |
| **Communication agent** | Every hour | Sends a personal follow-up to **Met** leads, detects replies, drafts an answer in Gmail and sets the lead to **Awaiting confirmation**. It never sends a reply on its own. |
| **Evening summary** | Weekday evenings | Emails you what happened today: new leads, emails sent, replies, drafts waiting for you. |

```
Apollo ──► Research agent ──► Notion "Lead Pipeline" ◄── Communication agent ◄──► Gmail
                                     │
                                     └──► Evening summary email
```

**Lead statuses:** New → Met → Sent → Awaiting confirmation → In conversation → Meeting booked (or Not interested / Unsubscribed)

---

## What you need

- **Claude desktop app** with Projects and scheduled tasks (Cowork)
- Connectors: **Apollo.io**, **Notion**, **Gmail** (Settings → Connectors)
- A second email inbox you control, for testing (demo mode)

The Apollo Free plan is enough for the demo. It includes the AI prospect search but not email reveals, which is why demo mode uses your test inbox.

---

## Repository structure

```
CLAUDE.md                        Project handbook: goals, target clients, statuses, rules
agents/research-agent.md         Research agent workflow (Phases 1–5) + evening summary (Phase 6)
agents/communication-agent.md    Communication agent workflow (Phases 1–5)
templates/follow-up-email.md     Follow-up email template with personal lines
compliance/us-email-rules.md     US email rules (CAN-SPAM) + house rules
setup/SETUP.md                   Step-by-step setup guide
setup/notion-schema.md           Notion database properties
logs/                            Each run writes a daily log here
```

---

## Quick start

1. Download this repository (**Code → Download ZIP**) and unzip it, e.g. into `Documents/Lead Gen Agent`.
2. Follow **[setup/SETUP.md](setup/SETUP.md)**: create the Notion database, fill in the `{{PLACEHOLDERS}}`, create a Claude Project and three scheduled tasks.
3. Click **Run now** on the research agent and watch leads appear in Notion.

---

## Key ideas

- **The logic lives in text files.** Each scheduled task has a one-line instruction ("Run the research agent"). To change what an agent does, edit its `.md` file.
- **Written for unattended runs.** Nobody is watching a scheduled run, so the agents never ask questions: they decide, log why, and continue.
- **Human in the loop where it matters.** Agents send the first follow-up only to people you marked **Met**, and replies are always drafts for your approval.
- **Demo mode** routes every email to your own test inbox, so you can try everything safely.

## Limitations

- Replies are picked up when the communication agent runs (every hour), not instantly.
- Your computer must be on with the Claude app open when tasks run, because they read this folder.
- The compliance file covers the US only. Check the rules for your country before real outreach.

## License

MIT. Use it, adapt it, share it.
