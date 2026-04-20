# Meeting Prep Coach

Week 7 of **52 Apps in 52 Weeks Before I Turn 52** by Hey I'm Papa.

Paste a meeting agenda or minutes and Claude returns 5–6 sharp, open-ended
coaching questions tailored to that content — hidden risks, assumptions,
blockers, misalignments.

## Running it

It's a single `index.html`. No build step, no install.

```
open index.html
```

Or serve the folder with any static server (e.g. `python3 -m http.server`).

## Using it

1. Click **Set API Key** and paste an Anthropic API key. It's stored only in
   this browser's `localStorage` and sent directly to the Anthropic API.
2. Optionally fill in **Your Role** (e.g. delivery lead, consultant) — it gets
   prepended to the prompt so questions can be tilted toward your vantage point.
3. Paste the meeting agenda or minutes into the textarea.
4. Hit **Analyse**. Questions stream in as the model produces them.
5. **Copy All Questions** copies a numbered list with rationale. **History**
   opens the last 5 sessions.

## How it works

- **Single file.** `index.html` loads React 18, Tailwind CSS and Babel
  Standalone from CDNs and renders a Babel-transformed `<script type="text/babel">`
  block. No bundler, no package.json.
- **Model.** `claude-sonnet-4-6` via the Anthropic Messages API
  (`anthropic-dangerous-direct-browser-access: true` so the call can be made
  from the browser).
- **Structured output via tool use.** The model is forced to call a
  `submit_questions` tool whose `input_schema` requires an array of 5–6
  `{question, context}` objects. This replaces a fragile
  `JSON.parse(text)` path — the shape is guaranteed by the schema.
- **Streaming.** `stream: true` on the request. A small progressive parser
  (`extractCompletedQuestions`) walks the partial JSON delivered in
  `input_json_delta` events and renders each question as soon as its object
  closes.
- **Prompt caching.** The system prompt is sent as a structured block with
  `cache_control: { type: "ephemeral" }`. For a prompt this short it's a no-op
  today (below the cache threshold), but the call is ready if the prompt grows.
- **Persistence.** The last 5 sessions and the API key are stored in
  `localStorage` under `meeting-prep-sessions` and `meeting-prep-api-key`. The
  key is trimmed on save and load to survive the usual copy-paste
  whitespace accidents into a `type="password"` input.

## Files

```
index.html   # the whole app
```

## Links

- Series: https://52-app.com/
- Repo: https://github.com/hayimpapa/week07_meeting_prep
