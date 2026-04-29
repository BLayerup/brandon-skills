---
name: conversational-questioning
description: Use this skill any time you are about to call AskUserQuestion. Brandon prefers clarification to feel like a conversation, not a form. Cap questions at 1 per round by default; 2 when tightly coupled; 3 only when batching genuinely saves a roundtrip. Never 4.
---

# Conversational Questioning

Brandon's pain point: asking 4 questions at once forces him to context-switch across all of them and respond in a single batch. The result feels like a survey. Ask fewer, more often. Use the rounds the conversation already provides instead of front-loading every uncertainty.

## The rule

When you are about to call `AskUserQuestion`:

**Default: 1 question.** If a single answer unlocks the next step, that is the only question to ask.

**2 questions allowed** when the answers are tightly coupled. "Tightly coupled" means Question B's options would shift depending on the answer to Question A, *and* asking them sequentially would waste a clear roundtrip. Independent questions are not tightly coupled, even if they're about the same project.

**3 questions max, ever.** Use only when all three are tightly coupled AND batching saves real time over sequential asking. If you cannot articulate in one sentence why all three must land together, ask fewer.

**Never 4.** The tool allows up to 4. This skill bans it.

## What this looks like in practice

Anti-pattern this skill exists to prevent:

> One AskUserQuestion call with 4 questions covering scope, format, audience, and depth.

Pattern to use instead:

> Round 1: ask about scope (1 question).
> User answers.
> Round 2: ask about format, now informed by scope (1 question).
> User answers.
> Proceed.

## Edge cases

**Multiple unrelated clarifications needed.** Ask them sequentially, one per round. Do not batch unrelated questions just because the tool supports it. An extra round costs less than a wall of questions.

**Question would be low-stakes.** Don't ask. State your assumption in the response and let Brandon correct course if it matters. His Global Instructions explicitly authorize judgment on low-risk and read-only items.

**User has already stated a preference.** Don't re-ask it. Apply the preference and proceed.

**Long task with many uncertainties.** Identify the *first* fork in the road that genuinely blocks progress and ask about that. Subsequent forks become questions when the conversation actually reaches them, not preemptively.

## Trigger

Apply this skill any time you would otherwise call `AskUserQuestion`. It governs *how* you ask, not *whether* you ask. Combine with Brandon's Global Instructions clause: state assumptions, seek approval for side effects, use judgment on low-risk and read-only operations.
