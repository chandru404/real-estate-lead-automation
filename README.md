# Real Estate Lead Capture & AI Qualification System

An end-to-end automation that captures property enquiries, 
qualifies leads using AI, and routes them to the right 
response channel — all within 10 seconds of form submission.

## The Problem It Solves

Indian real estate brokers receive leads from multiple sources 
(99acres, MagicBricks, Meta ads, website forms) and lose up to 
70% of them due to slow follow-up. Most brokers reply manually, 
often hours later — by which time the buyer has already spoken 
to a competitor.

This system ensures every lead gets an instant response and 
is intelligently routed based on buying intent.

## How It Works

1. Lead submits enquiry form
2. n8n webhook receives data instantly
3. Fields are normalised from any source format
4. GPT-4o-mini qualifies the lead (score 0–100)
5. Lead saved to Google Sheets CRM with enriched data
6. HOT leads (70+) → instant Telegram alert + personalised email
7. WARM leads (40–69) → nurture email
8. COLD leads (<40) → soft email, no broker interruption

## Tech Stack

- n8n (workflow automation)
- OpenAI GPT-4o-mini (lead qualification)
- Google Sheets (CRM / data store)
- Telegram Bot API (instant notifications)
- Gmail (automated email responses)
- HTML/JS (lead capture form)

## Key Technical Decisions

**Why HTML over Markdown in Telegram:**
AI-generated text contains unpredictable special characters 
(dashes, brackets, parentheses) that break Telegram's Markdown 
parser. HTML parse mode only activates on explicit tags, 
making it reliable for dynamic content.

**Why a dedicated Code node for parsing:**
LLM output is a raw string — even when it contains JSON. 
The Code node uses JSON.parse() inside a try/catch block 
to convert it into a usable object and gracefully handle 
any malformed AI responses without crashing the workflow.

## AI Qualification Logic

The system prompt instructs GPT to score leads on:
- Budget clearly stated above ₹40L: +25 points
- Purchase timeline under 3 months: +25 points
- Phone number provided: +20 points
- BHK preference stated: +15 points
- Self-use intent (vs vague): +15 points

## Results (Demo)

Tested with 3 lead types:
| Lead Type | Score | Category | Response |
|-----------|-------|----------|----------|
| Confirmed buyer, 1 month | 85 | HOT | Telegram + urgent email |
| Interested, 3-6 months | 52 | WARM | Nurture email |
| Just browsing, no details | 22 | COLD | Soft email only |

## Setup Instructions

1. Import `workflow/re_lead_capture_v1.json` into n8n
2. Add credentials: OpenAI, Google OAuth2, Telegram Bot
3. Update Google Sheet ID in the Sheets node
4. Update Telegram Chat ID
5. Open `form/index.html` and replace webhook URL
6. Activate workflow
