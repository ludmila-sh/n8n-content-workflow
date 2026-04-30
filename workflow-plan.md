# Workflow Plan — v1

## Google Sheets Schema

Sheet name: `content-ideas`

| Column | Field name | Type | Notes |
|--------|------------|------|-------|
| A | `id` | number | Row number (n8n uses this to update rows) |
| B | `topic` | text | The content idea (required) |
| C | `goal` | text | What should this content achieve? (1 line, optional) |
| D | `status` | text | `new` → `drafted` → `approved` |
| E | `draft_caption` | text | Instagram caption (filled by workflow) |
| F | `draft_script` | text | Short-form video script (filled by workflow) |
| G | `visual_ideas` | text | 2 image prompt ideas (filled by workflow) |
| H | `drafted_at` | datetime | Filled by workflow when drafts are written |
| I | `notes` | text | Human review comments (filled manually) |

### Example test row
| id | topic | goal | status |
|----|-------|------|--------|
| 1 | How to fall asleep faster without medication | Help people with sleep anxiety feel less alone | new |
| 2 | Why your brain resists new habits | Explain the science simply, give one practical tip | new |

---

## Node-by-Node Design

### Node 1 — Manual Trigger
- Type: `Manual Trigger`
- Purpose: Start the workflow on demand
- No configuration needed

### Node 2 — Read Sheet Row
- Type: `Google Sheets` → operation: `Get Many Rows`
- Sheet: `content-ideas`
- Filter: `status` equals `new`
- Limit: 1 (only process one row per run)
- Sort: by row number ascending (oldest first)

### Node 3 — IF: Row Found?
- Type: `IF`
- Condition: check that the output from Node 2 is not empty
- True branch → continue to LLM
- False branch → Stop (log "no new rows")

### Node 4 — Call LLM
- Type: `OpenAI` → Chat Message
- Model: `gpt-4o`
- System prompt: see `prompts.md`
- User message: inject `topic` and `goal` from the row
- Response format: JSON (set in OpenAI node or via prompt instruction)

### Node 5 — Parse LLM Output
- Type: `Code` (JavaScript)
- Purpose: extract `draft_caption`, `draft_script`, `visual_ideas` from the JSON response
- Also set `drafted_at` to current timestamp

```javascript
const raw = $input.first().json.message.content;
const parsed = JSON.parse(raw);

return [{
  json: {
    draft_caption: parsed.draft_caption,
    draft_script: parsed.draft_script,
    visual_ideas: parsed.visual_ideas,
    drafted_at: new Date().toISOString(),
  }
}];
```

### Node 6 — Update Sheet Row
- Type: `Google Sheets` → operation: `Update Row`
- Match row by: `id` column
- Write fields: `draft_caption`, `draft_script`, `visual_ideas`, `drafted_at`, `status` = `drafted`

### Node 7 — Send Telegram Message
- Type: `Telegram`
- Chat ID: your personal chat ID
- Message format: see below

```
New draft ready for review

Topic: {{topic}}
Status: drafted

CAPTION:
{{draft_caption}}

SCRIPT:
{{draft_script}}

VISUAL IDEAS:
{{visual_ideas}}

Review in Google Sheets: [link]
```

---

## Data Flow Summary

```
Manual Trigger
  → Read 1 row (status=new)
  → IF row exists
      → Call LLM with topic + goal
      → Parse JSON response
      → Update row (drafts + status=drafted)
      → Send Telegram message
  → ELSE: Stop
```

---

## Known Limitations (v1)

- Processes only one row per trigger. Run again for the next row.
- No automatic scheduling yet (add a Schedule Trigger in v2).
- If the LLM returns malformed JSON, the Code node will fail. Handle in v2.
- No retry logic.
- Telegram message is plain text. Could be formatted better in v2.

---

## Future Improvements (out of scope for v1)

- Schedule Trigger (e.g., run daily at 9am)
- Error handling for malformed LLM output
- `approved` → `rejected` status flow
- Multiple rows per run
- Separate sheets per content type
- Image generation from `visual_ideas` via DALL-E or Ideogram
