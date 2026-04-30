# Notes — Decisions, Debugging, Lessons Learned

---

## Session log

### 2026-04-30 — Project kickoff

**Decisions made:**
- Using Google Sheets as both source and output store. Simple enough for v1, no database needed.
- Using OpenAI GPT-4o via n8n's built-in OpenAI node. Avoids raw HTTP requests for now.
- Manual Trigger only in v1. Schedule Trigger is v2.
- One row per run. Keeps the workflow simple and debuggable.
- JSON output format from LLM. Easier to parse than section markers in n8n.
- Telegram for notifications (not email). Faster to set up, easier to test.

**Open questions:**
- What Telegram bot to use? (need to create via BotFather if not done yet)
- Which OpenAI API key to use for this project?
- n8n instance: self-hosted or cloud? (affects credential setup)

**Next step:**
- Create the Google Sheet with the schema from `workflow-plan.md`
- Add 2 test rows with `status = new`
- Connect n8n to the sheet and confirm data reads correctly

---

## Debug log

_Add debugging notes here as you work through the nodes._

---

## Lessons learned

_Add things that surprised you or that you would do differently._
