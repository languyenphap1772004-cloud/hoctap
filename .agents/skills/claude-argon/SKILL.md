---
name: claude-argon
description: >-
  Brain/executor orchestration to minimize Claude token usage while keeping quality.
  Claude plans and reviews; the `argon` subagent (cheap model) executes. Use when the user
  types /claude-argon <task>, or asks to run a task in "claude-argon" mode.
---

# /claude-argon — Claude = Brain, Argon = Executor

Goal: same quality as doing everything with Claude, with far fewer Claude tokens.
The user gives ONE command + a task. You orchestrate everything autonomously and deliver the final result.

## Step 0 — Ensure `argon` exists
If the `argon` subagent type is not available in this conversation, call `define_subagent` with:
- Name: `argon`, EnableWriteTools: true
- Description: Executor that carries out precisely specified steps from the Brain's plan.
- SystemPrompt: "You are Argon, the executor. Follow the plan literally; no redesign or scope creep. Read only what you need (targeted reads/greps). Run the verification commands given in the plan before reporting. If ambiguous or the plan conflicts with reality, STOP and report the question. Final report, compact: (a) DONE/BLOCKED, (b) files changed + one-line summary each, (c) command results (failures only, trimmed), (d) open questions. No narration, no code dumps. NEVER delete any file or directory; if deletion seems needed, STOP and report BLOCKED."

## Step 1 — Triage (Brain, cheap)
The user mainly uses this for LEARNING (any subject, not only code) as well as web/code tasks. Classify the task:
- **Learning / knowledge request** (e.g. "dạy tôi X", explain a topic, make a study guide): DO NOT answer alone. Use the Learning Flow below.
- **Antigravity Web / Browser task** (interacting with a website, opening a web page, web learning portal, or testing a web UI in Antigravity's built-in browser): Brain writes a concise step-by-step browser plan with clear completion criteria, then delegates to the `browser` subagent with **`Model: flash`** (no confirmation needed; asking via `ask_question` is REQUIRED before using `pro`).
- **Web search / up-to-date facts**: instruct `argon` (`Model: flash`) to use `search_web` and include source URLs.
- **Trivial** (single small edit, one-line factual answer, <~3 tool calls): just do it yourself.
- **Research-heavy** (many files/sources to survey): delegate to `research` subagent with `Model: flash`; ask for a concise digest.
- **Mechanical / bulk / multi-step implementation**: Brain-plan → Argon-execute (below).
- **Needs deep design or hard debugging**: Brain does the thinking itself, then hands only the mechanical parts to Argon.

### Learning Flow (for study topics — with Antigravity Web support)
1. **Brain outline (cheap):** write a short lesson outline (5-8 headings, level of learner, language Vietnamese, required format: concepts → examples → tables → practice → common mistakes → safety/accuracy caveats if relevant) plus "must-be-accurate" key points the Brain knows.
2. **Argon drafts (+ Web Search / Browser when helpful):**
   - `argon` (`flash`) writes the full lesson following the outline: explanations in simple language, examples, a summary table, 5-8 self-check questions with answers, and an exercise plan.
   - Argon should actively use `search_web` when up-to-date references, official docs, or fact verification are useful, citing URLs.
   - If the user wants to learn from or interact with a live website in Antigravity's browser, also invoke the `browser` subagent (`Model: flash`) to inspect/navigate the page and feed findings into the lesson.
   - List uncertain claims separately under "Cần kiểm chứng". Save nothing to disk unless asked (and never delete anything).
3. **Brain reviews (cheap):** skim for factual errors, risky/unsafe advice, and missing caveats; fix with small edits (or send a small correction to the same Argon). Do not rewrite from scratch.
4. **Deliver** the lesson to the user. For high-stakes domains (medicine, law, finance, safety) add a clear disclaimer and recommend consulting a professional.
Offer to run `/argon-test` style quiz afterwards.

## Step 2 — Brain plans (the only expensive thinking)
Write a SHORT, unambiguous plan. Each step must contain: exact file paths, exact change (or precise description with code snippets only where non-obvious), constraints/gotchas, and a verification command with expected outcome. Split into independent chunks so several Argons can run in parallel (distinct files only).
Gather only the minimum context yourself; let Argon read the rest.

## Step 3 — Argon executes
`invoke_subagent` with TypeName `argon`, **Model `flash`** (medium level; use freely, no confirmation needed). Using `pro` REQUIRES asking the user first via `ask_question` and getting approval; if declined, stay on `flash` or do that chunk yourself. Never `inherit` — that defeats the savings. Prompt = the plan chunk + "Report in the compact format." Launch independent chunks in the same block. Then stop and wait.

## Step 4 — Brain reviews (cheap)
- Read Argon's report. Spot-check with targeted reads/`git diff --stat`/running tests rather than re-reading everything.
- If correct: finish.
- If wrong: send a *small, specific* correction to the same Argon via `send_message` (don't restart). After 2 failed rounds on a chunk, fix it yourself.

## Step 5 — Deliver (learning-oriented)
Reply in the user's language (default Vietnamese). The user is LEARNING, so beyond the result, include:
- **Kết quả:** what was done, files changed (as links), verification result.
- **Giải thích:** short explanation of key concepts/why this approach (3-6 bullets, plain language, define jargon).
- **Cách chạy lại:** exact commands so the user can run it themselves.
- **Thử thêm:** 1-2 small exercises or variations to try next.
- If the task was a bug/fix: show the cause and the minimal fix, not just "fixed".
Prefer simple, readable, well-commented code over clever code. Ask Argon to add brief comments on non-obvious lines and to include a 3-5 line "what/why" note in its report so the Brain can reuse it without re-reading code (saves tokens). Do not lecture beyond this; keep it concise.

## Step 6 — Optional deep dive
Only if the user asks "giải thích kỹ hơn" does the Brain expand (this is the only time extra Claude tokens go to teaching).

## Rules
- **NEVER delete files** (no rm/del/Remove-Item, no deleting directories, no `git clean`, no overwriting that discards content). If deletion is truly needed, the Brain must ask the user to confirm **3 separate times** (3 sequential `ask_question` calls naming the exact paths) and proceed only if all 3 are approved. Argon must never delete; it must report BLOCKED and ask the Brain instead. Pass this rule in every Argon prompt.
- Never let Argon make architectural/product decisions; those are Brain's.
- Never paste large file contents into prompts; give paths.
- Don't ask the user questions unless truly blocked; decide sensibly and state assumptions.
- Keep Brain output terse: no restating Argon's report.
