---
name: conversational-questioning
description: Conversational style is task-shaped. For short conversational tasks, cap questions at 1 per round by default, 2 when tightly coupled, 3 only when batching saves a roundtrip, never 4. For multi-step build / refactor / research sessions (4+ files, 2+ subsystems, or > 30 min of work), switch to plan-first mode — front-load assumptions and questions in a written plan, get MODE EXECUTE, execute without interruption.
---

# Conversational Questioning

Brandon's pain point: asking 4 questions at once forces him to context-switch across all of them and respond in a single batch. The result feels like a survey. Ask fewer, more often. Use the rounds the conversation already provides instead of front-loading every uncertainty.

## When to ask vs. when to plan

This skill governs *when* and *how many* questions to ask. The right shape depends on the task type.

### Short conversational tasks (default)

For quick exchanges — a question, a small one-step ask, a clarification — keep the existing incremental rule:

**Default: 1 question.** If a single answer unlocks the next step, that is the only question to ask.

**2 questions allowed** when the answers are tightly coupled. "Tightly coupled" means Question B's options would shift depending on the answer to Question A, *and* asking them sequentially would waste a clear roundtrip. Independent questions are not tightly coupled, even if they're about the same project.

**3 questions max, ever.** Use only when all three are tightly coupled AND batching saves real time over sequential asking. If you cannot articulate in one sentence why all three must land together, ask fewer.

**Never 4.** The tool allows up to 4. This skill bans it.

This works because the cost of being wrong is low and clarification turn-around is fast.

### Multi-step build / refactor / research sessions

For sessions that involve building, refactoring, or researching across multiple files or hours, switch to a plan-first model:

0. **Search for existing planning artifacts first.** Before drafting any new plan, check whether one already exists. Common locations:
   - `~/Documents/Claude/Projects/*/SKILLS_PLAN_*.md` (portfolio-wide skills planning)
   - `~/Documents/Claude/Projects/*/_handoff_*.md` (cross-project handoffs)
   - The project directory itself for `PROPOSAL_*.md`, `HANDOFF.md`, `PLAN.md`, `ROADMAP.md`
   - `~/Desktop/About_Me/Memory.md` change log for prior session context

   If a plan exists, read it before drafting your own. Reconcile rather than duplicate.

1. **Front-load assumptions and questions in a written plan**, not in chat.
2. **State assumptions explicitly** in the plan (`ASSUMPTION:` lines or a section).
3. **Ask 0–2 questions in chat only when an assumption is genuinely undecidable** without user input — and when you do, batch them.
4. **Get MODE: EXECUTE on the plan**, then execute without further interruption unless something materially changes.
5. **Surface mid-task discoveries** in the plan doc (or a note section), not as chat questions, unless the discovery requires a decision.

The trigger to switch into plan-first mode: the request is going to involve more than ~3 file edits, more than ~30 minutes of work, or more than one tool category (research + code, code + tests, etc.).

### Why both modes exist

Incremental questions are kind during conversation. Plan-first is kind during execution — every interruption to ask "and which way did you want X?" breaks flow and costs more time than it saves on a long task.

### How to recognize the mode

| Signal | Mode |
|---|---|
| User asks a factual question | Incremental |
| User asks for a small one-shot ("rename this var", "what does this error mean") | Incremental |
| User describes a multi-step goal ("build an X that does Y") | Plan-first |
| User says "draft a plan" / "write a proposal" / "what would you recommend" | Plan-first |
| Task touches 4+ files OR 2+ subsystems OR will run > 30 min | Plan-first |
| Task is research + synthesis | Plan-first |

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
