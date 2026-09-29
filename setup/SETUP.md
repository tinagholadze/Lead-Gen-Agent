# Setup guide

About 30 minutes. No code.

## 1. Connect your tools

In the Claude desktop app, open **Settings → Connectors** and connect:
- **Apollo.io** (the Free plan works)
- **Notion**
- **Gmail**

## 2. Create the Notion database

1. In Notion, create a full-page database called **Lead Pipeline** with the properties in [notion-schema.md](notion-schema.md).
2. Add a **Board** view grouped by **Status** and name it **Pipeline board**. This is your live dashboard.
3. Copy two things for step 3:
   - the database **data source ID** (ask Claude in a chat: "Fetch my Notion database Lead Pipeline and give me its data source ID"),
   - the **Pipeline board view URL** (open the view, copy its link).

Tip: you can also ask Claude to create the database for you: "Create a Notion database with the properties in setup/notion-schema.md".

## 3. Fill in the placeholders

Open each `.md` file and replace every `{{...}}`:

| Placeholder | Example |
|---|---|
| `{{YOUR_NAME}}` | Alex Smith |
| `{{YOUR_BUSINESS}}` | Smith AI Consulting |
| `{{YOUR_POSTAL_ADDRESS}}` | your business address (required by US email law) |
| `{{YOUR_GMAIL}}` | the Gmail account connected to Claude |
| `{{YOUR_TEST_INBOX}}` | a second inbox you control, used in demo mode |
| `{{YOUR_TIMEZONE}}` | Europe/Berlin |
| `{{NOTION_DATA_SOURCE_ID}}` | collection://… |
| `{{NOTION_VIEW_URL}}` | the Pipeline board view link |
| `{{EVENT_1}}`, `{{EVENT_2}}`, `{{EVENT_3}}` | demo event names, e.g. "AI Ops Summit, Austin" |

Also adjust **"Who we are looking for (ICP)"** in `CLAUDE.md` to your own ideal clients.

## 4. Create the Claude Project

1. In the Claude app, create a new Project called **Lead Gen Agent**.
2. **Folder:** attach this folder.
3. **Instructions:** paste:

```
This is the Lead Gen Agent project: a recurring, unattended lead-generation system.
All rules and workflows live in the project folder. Always read CLAUDE.md first, then the file for the task:
- Research agent → agents/research-agent.md (Phases 1–5)
- Evening summary → agents/research-agent.md (Phase 6 only)
- Communication agent → agents/communication-agent.md
Every email must pass compliance/us-email-rules.md.
Runs are unattended and may be started any time with "Run now": never ask questions, decide, log the reason in logs/YYYY-MM-DD.md, and continue.
```

## 5. Create three scheduled tasks

Inside the Project, click **+** next to **Scheduled** three times:

| Name | Instructions | Repeats |
|---|---|---|
| Research agent | `Run the research agent (Phases 1–5 of agents/research-agent.md).` | Weekdays, 07:53 |
| Communication agent | `Run the communication agent (agents/communication-agent.md).` | Weekdays, hourly 09:00–19:00 |
| Evening summary | `Run the evening summary (Phase 6 of agents/research-agent.md).` | Weekdays, 18:27 |

For each task:
- attach this **folder** (open the task and check it is listed, otherwise the agent cannot read its instructions),
- set permissions to **Automatically approve**.

## 6. Test it

1. **Research agent → Run now.** New leads appear in the **New** column of your Pipeline board, and a log appears in `logs/`.
2. Change one lead to **Met**.
3. **Communication agent → Run now.** The follow-up email arrives in your test inbox; the lead moves to **Sent**.
4. Reply from your test inbox, then **Run now** again. A draft appears in Gmail and the lead moves to **Awaiting confirmation**.
5. Send the draft yourself. The next run moves the lead to **In conversation**.
6. **Evening summary → Run now.** You get the summary email.

## Going live

When you're ready for real outreach:
1. Set **Demo mode** to OFF in `CLAUDE.md`.
2. Use a paid Apollo plan (email reveals), or add emails yourself when you mark a lead **Met**.
3. Check the email rules for every country you contact.

## Troubleshooting

| Problem | Fix |
|---|---|
| A run finishes in seconds and does nothing | The task has no folder attached. Attach this folder in the task settings. |
| "Usage limit" error from Notion | The agents must read Notion in **view mode** with `{{NOTION_VIEW_URL}}` (already in CLAUDE.md). |
| Apollo `API_INACCESSIBLE` | Your Apollo plan blocks that endpoint. Stay in demo mode. |
| Scheduled run didn't happen | Your computer was asleep or the Claude app was closed. |
