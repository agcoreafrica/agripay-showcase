# Highlight 03 — AI copilot with propose-only guardrails

A streaming, tool-using copilot that shares one guarded execution core with the rest of the
platform — and can only ever *propose*, never *decide*.

## Why it is interesting

The copilot is not a separate chatbot bolted onto the product. Both the in-app server action and
the public SSE chat route drive the **same** runner through the **same** propose-only envelope:
cost cap, rate limit, and an audit proposal on every run. Tool results flush live over SSE as
`tool_use_result` frames (and as voice cards in voice mode) before the final answer streams —
so users watch the copilot *work*, not just read its conclusion.

## The route

Source: `app/api/agent/chat/route.ts` (abridged)

```ts
// POST /api/agent/chat — SSE stream for the assistant copilot.
//
// The voice-mode client (and any streaming chat client) POSTs an
// AssistantChatRequest; the route runs the SAME proposeOnly-wrapped
// assistant runner the server action uses (cost cap, rate limit, audit
// proposal), and streams ChatSseEvent frames as `data: {json}\n\n`.
//
// The assistant runner is NOT token-streaming today, but its tool loop
// exposes an onToolResult hook. We start the runner INSIDE the stream's
// start() so tool events flush live (tool_use_result, and in voice mode a
// single voice_card for the first tool that yields one) BEFORE the final
// answer is chunked into text_delta frames. See lib/voice/types.ts for the
// authoritative ChatSseEvent + AssistantChatRequest contract.
```

## The shared runner

The same core drives both entry points, so guardrails cannot be bypassed by choosing a different
door. Per-role principal resolution means the copilot inherits exactly the caller's authorization
context — it never holds credentials of its own.

Source: `lib/ai/agents/assistant-runner.ts` (abridged)

```ts
// Shared server-side core for the global assistant copilot.
//
// Extracted from actions/ask-assistant.ts so BOTH the server action
// (askAssistantAction) and the SSE chat route (app/api/agent/chat) drive
// the assistant through the SAME proposeOnly envelope (cost cap, rate
// limit, audit proposal) and the SAME per-role principal resolution.
//
// askAssistantAction is now a thin wrapper over runAssistantQuery(); its
// external behaviour, exports, and tests are unchanged. The only additive
// surface is `voice` — voice-mode fields + an onToolResult callback the
// route threads down to the copilot runner.
```

## How proposals become safe

When the copilot proposes a consequential action, the proposal is converted into a durable draft
with the payload-hash, four-eyes, and single-claim guarantees described in
[02-evidence-and-four-eyes.md](02-evidence-and-four-eyes.md). The autonomy lanes in the action
catalog decide what the copilot may stage (`auto_routine` → `human_consequential`), and unknown
actions resolve to the most restrictive lane.

## Supporting machinery

- **Cost discipline:** a worst-case tool-loop budget is reserved up front against a daily cap, so
  a runaway tool loop cannot spend unbounded.
- **Voice mode:** locale-aware (English, Yoruba, Hausa, Igbo), with a shared SSE event contract
  and voice-card rendering for tool results — the contract module is deliberately import-safe from
  both server and client.
- **Agent drafts with eval harnesses:** the agent layer has its own eval runner runnable in CI
  (see [06-testing-and-ci.md](06-testing-and-ci.md)).
