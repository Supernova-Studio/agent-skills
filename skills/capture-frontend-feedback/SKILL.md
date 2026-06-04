---
name: capture-frontend-feedback
description: Use when frontend, UI, design-system, token, styling, motion, responsive, or accessibility work hits friction because design-system context, or its absence, caused issues such as missing component documentation, incorrect token values, conflicting context, the assistant guessing at a pattern, user reformulating, or explicit correction.
---

# Capture frontend feedback

Persist friction seen during real frontend or design-system work. Make one `collect_agent_feedback` call per capture. Do not write local files.

Use this only when the `collect_agent_feedback` tool is available. If a capture attempt fails because feedback collection is not enabled, tell the user and continue without retrying.

## Capture when

The work is frontend-adjacent and one signal fires:

- `retrieval_miss`: relevant design-system context was missing or too thin to answer safely. Includes situations where you had to extrapolate from general knowledge instead of grounding in design system content.
- `conflicting_context`: multiple plausible patterns or values exist and the current source of truth does not resolve them. e.g. Modal vs Dialog, two spacing scales, two ways to consume tokens etc.
- `user_reformulated`: the user asks again with tighter wording because the first answer missed the target.
- `user_correction`: the user explicitly says the answer or pattern was wrong.
- `no_context_used`: the session iterates on clear design-system concerns without consulting a source of truth.

Related scopes include components, tokens, styling, layout, responsive behavior, motion, accessibility, design-doc rendering, Figma/code alignment, or frontend MCP/tooling gaps.

## Skip when

- The work is not meaningfully frontend or design-system related.
- No signal fired.
- The issue is just a normal code bug unrelated to design-system context.
- The user is simply planning, chatting, or asking for explanation.

## Tool guidance

Use the `collect_agent_feedback` tool schema for the top-level payload. This skill only standardizes when to capture, how to scope the transcript, and what to put in `metadata.capture`.

## `metadata.capture`

Keep this small. Do not duplicate top-level tool fields inside `metadata.capture`.

```json
{
  "signal": "retrieval_miss | conflicting_context | user_reformulated | user_correction | no_context_used",
  "why_flagged": "One plain sentence describing the observed friction.",
  "user_query": "Short task framing from the user.",
  "expected_ds_content": "What the design system should have provided, or \"\" if unknown.",
  "what_you_actually_did": "How the answer was produced despite the gap.",
  "affected_entities": ["Dialog", "spacing token"],
  "action_items": [
    {
      "surface": "mcp | documentation | studio | exporter | skill | unknown",
      "description": "Concrete follow-up.",
      "acceptance_criteria": "How a reviewer knows this is done."
    }
  ],
  "evidence": [
    {
      "kind": "repo_file | repo_glob | url | mcp_tool | token_path | command | other",
      "ref": "path, URL, tool, or command",
      "note": "optional"
    }
  ]
}
```

Rules:

- Include at least one `action_items` entry.
- Prefer 1-3 `affected_entities`.
- Add `evidence` when you have a concrete pointer; otherwise leave it empty.

## Transcript rules

- Include only turns relevant to the friction.
- Keep user lines verbatim.
- Keep assistant lines as the real reply text; collapse pure tool work to bracketed summaries if needed.
- Aim for 4-15 turns. Hard cap: 40.
- If the exact assistant text is unavailable, use `assistant: [Original assistant message not available]`.

## Recommended mapping

- `retrieval_miss` usually maps to missing information.
- `conflicting_context` usually maps to incorrect or missing information.
- `user_reformulated` is usually neutral sentiment unless the user is clearly frustrated.
- `user_correction` often means the agent output was wrong, the source was wrong, or the guidance was incorrect.
- `no_context_used` often means the wrong source was used or the right source was skipped.

Pick the top-level category and sentiment that best explain the failure, using the tool schema definitions.

## Workflow

When a signal fires:

1. Write a short title for the feedback tool call.
2. Build the relevant `conversation`.
3. Fill `metadata.capture`.
4. Complete the remaining top-level tool fields from the tool schema.
5. Call `collect_agent_feedback`.
6. If successful, end the user-facing reply with `Captured: signal=<signal>, category=<category>`.
7. If the tool says feedback collection is not enabled, tell the user briefly and move on.

Multiple distinct signals in one turn can produce multiple MCP calls.
