# AI Content Workflow — n8n Sample #1

A simple, human-in-the-loop automation that takes a content idea from Google Sheets, generates draft social media content using an LLM, saves the results back to the sheet, and sends a Telegram notification for review.

## What it does

1. Reads one content idea row from Google Sheets (where `status = new`)
2. Sends the topic and goal to an LLM
3. Generates: Instagram caption draft, short-form video script draft, 2 visual ideas
4. Writes all drafts back to the row and updates `status` to `drafted`
5. Sends a Telegram message with the output for human review

Human review is required before any content is published. This is intentional.

## Stack

| Component | Tool |
|-----------|------|
| Workflow engine | n8n (self-hosted or cloud) |
| Data source + output | Google Sheets |
| Content generation | OpenAI GPT-4o (via n8n node) |
| Review notification | Telegram Bot |

## Status

v1 — in progress.

## How to run (after setup)

1. Open the workflow in n8n
2. Click **Test workflow** (Manual Trigger)
3. Check your Google Sheet — the drafted row should be updated
4. Check Telegram — you should receive a summary message

## Setup

_Coming after first working milestone. Will include: credentials setup, Google Sheet template link, workflow JSON import instructions._

## Scope

**v1 includes:** read from sheet, LLM draft generation, write back to sheet, Telegram notification, status tracking.

**v1 does not include:** direct posting to Instagram or TikTok, video generation, multi-agent orchestration, analytics.

## Portfolio note

This is Sample #1 in a small portfolio of practical AI automations. Built to demonstrate real client-ready workflow logic without over-engineering.
