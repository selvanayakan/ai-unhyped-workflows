# Model Bench: GPT-6 Astra vs Gemini 4 Argon

The n8n workflow from the video **"GPT-6 vs Gemini 4 Argon: same score, which one actually wins?"** (Selva | AI Unhyped).
Video: _link added after upload_

It sends **the same test cases to two AI models, one at a time**, and logs for every answer: the time taken, tokens (thinking included), cost at today's and the announced December price, and a score wherever there is a right answer.

All test data is **fictional** (made-up companies in India and the US).

## What's inside

| Path | What it is |
|---|---|
| `model-bench-workflow.json` | The n8n workflow (import this) |
| `test-data/support-emails.json` | 20 customer emails with the answer key (chapter 4) |
| `test-data/client-email.txt` | The messy client email (chapter 2) |
| `test-data/client-email-checklist.md` | The yes/no checklist we scored chapter 2 with |
| `test-data/invoices/` | 3 invoice images + answer key (chapter 3) |
| `our-results/` | Our raw results from 15 Oct 2026: every prompt, answer, time, token count and cost |

## Setup (about 10 minutes)

1. In n8n: **Workflows → Import from file** → `model-bench-workflow.json`.
2. **GPT-6 Astra** node → Credential: *Header Auth*, Name `Authorization`, Value `Bearer YOUR_OPENAI_API_KEY`.
3. **Gemini 4 Argon** node → Credential: *Header Auth*, Name `x-goog-api-key`, Value `YOUR_GEMINI_API_KEY` (Gemini 4 Argon needs a paid API tier).
4. Chapter 3 only: upload the 3 invoice images to your Google Drive (any folder, keep the file names) and connect Google Drive in **Find Invoice** and **Download Invoice**.
5. In **Test Cases**, set `CHAPTERS` (default `['4']`):
   - `'1'` 5 questions about things that don't exist
   - `'1b'` 6 questions with one false detail
   - `'2'` the client email → requirements
   - `'3'` invoices → 8 fields as JSON (run on its own)
   - `'4'` 20 support emails → category, urgency, reply
6. Click **Execute workflow**. When it finishes, open **Score** → *Binary* → download `results.json`. The *JSON* tab shows the summary.

**Test your own models:** change `MODELS` in **Test Cases** (model id, provider, prices per 1M tokens). The OpenAI node uses the Responses API, the Gemini node uses `generateContent`.

## Costs (please read)

Every run calls paid APIs. What our runs cost on 15 Oct 2026:

| Run | GPT-6 Astra | Gemini 4 Argon |
|---|---|---|
| Chapter 4 (20 emails) | about $0.10 | about $0.02 |
| Chapter 3 (3 invoices) | about $0.09 | about $0.01 |
| Chapter 2 (1 long answer) | about $0.10 | about $0.02 |

Prices change. Argon's launch price ($2 / $10 per 1M tokens) rises to $4 / $20 on 1 Dec 2026; update the prices in `MODELS` so the cost columns stay correct.

## Settings we used

- Reasoning **medium** on both (`reasoning.effort` for OpenAI, `thinkingLevel` for Gemini).
- One run per test case. Run it again and some answers will change.
- Chapter 2 allows up to 12,000 output tokens (long answers); everything else 6,000.

## Known limits and what went wrong

- **Our answer key** marked two emails (E3, E16) as normal urgency, but our own rule ("money currently blocked") supports high. Both models said high. That's our mistake, not theirs.
- **Code fences:** Gemini wrapped its JSON in ```json fences although the prompt said "No other text". The scorer strips fences before checking, so those answers count as valid JSON. A strict parser would reject them.
- **Chapter 2 is not scored by code.** Requirements need human judgment, so we scored them with the yes/no checklist in `test-data/`.
- **Chapter 1 scoring** (did it invent things?) was done by hand from the answers in `results.json`.
- Small sample: 1–20 cases per chapter, one run each. Treat it as a practical check, not a benchmark.

## Licence

MIT: use it, change it, share it. A mention of the channel is appreciated, not required.
No warranty: you pay for your own API usage.
