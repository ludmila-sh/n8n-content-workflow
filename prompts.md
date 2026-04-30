# Prompts — Version History

---

## v1 — Initial prompt (2026-04-30)

### System message

```
You are a content assistant helping a solo creator draft social media content.
Your job is to generate clear, practical drafts — not hype, not generic filler.
Write as if you understand the topic, not as if you are selling something.
```

### User message (with n8n expressions)

```
TOPIC: {{ $json.topic }}
GOAL: {{ $json.goal }}

Generate content for Instagram and short-form video based on the topic and goal above.

Return ONLY valid JSON. No markdown. No explanation. No extra text. Just the JSON object below:

{
  "draft_caption": "Instagram caption. Max 220 characters. Include 3 to 5 relevant hashtags at the end.",
  "draft_script": "Short-form video script. 6 to 10 lines. Natural spoken tone. Under 60 seconds when read aloud. No stage directions.",
  "visual_ideas": "Idea 1: [description]; Idea 2: [description]"
}
```

### Why this format

- JSON output is easy to parse in n8n's Code node with `JSON.parse()`
- Explicit character limits prevent captions that are too long to post
- Script length constraint keeps it realistic for Reels / TikTok
- Two visual ideas is enough to be useful without overwhelming
- "No markdown / no explanation" reduces the chance of the model wrapping the JSON in a code block

### Known issues with v1

- If the model adds a markdown code block around the JSON (` ```json ... ``` `), `JSON.parse()` will fail
- Mitigation: in the Code node, strip backtick fences before parsing (see `workflow-plan.md` Node 5)
- `goal` is optional in the sheet; if it is blank, the prompt still works but output may be more generic

---

## Planned for v2

- Add tone guidance (e.g., "calm and factual", "warm and personal")
- Add audience field (e.g., "adults with sleep issues")
- Test structured output via OpenAI's `response_format: { type: "json_object" }` to guarantee valid JSON
