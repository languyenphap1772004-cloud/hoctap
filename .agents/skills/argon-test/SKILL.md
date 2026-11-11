---
name: argon-test
description: >-
  Pure-Argon testing/QA skill. Runs a verify-and-fix loop entirely with the `argon` subagent
  (cheap model) to maximize the quality of Argon's output: writes/finds tests, runs them,
  fixes failures, and self-reviews against a checklist. Use when the user types /argon-test <task or target>,
  or wants Argon's work tested/hardened. Can be used standalone or as the verification phase of /claude-argon.
---

# /argon-test — Argon-only verification loop

Goal: get the best possible result out of Argon through systematic testing, with minimal Claude involvement.
The main agent acts ONLY as a thin dispatcher: it launches Argon with the protocol below, waits, and relays the final report. It does no analysis itself.

## Dispatch
1. If `argon` subagent type is missing, define it (EnableWriteTools: true; system prompt: follow instructions literally, compact reports, stop and ask if ambiguous).
2. Choose executor by target:
   - **Code / files / knowledge & web fact-check:** `invoke_subagent` TypeName `argon`, Model `flash` (medium level; no confirmation needed). Argon can use `search_web` directly to verify facts on the web.
   - **Live Website / Web App in Antigravity's Browser:** `invoke_subagent` TypeName `browser`, Model `flash` (medium level; no confirmation needed) with a structured UI/functional test checklist (happy path, edge inputs, visual/console errors, completion criteria).
   - Escalating either `argon` or `browser` to `pro` is allowed ONLY if `flash` fails twice on the same issue AND ONLY after asking the user via `ask_question` and getting approval; if declined, stay on `flash` or report BLOCKED.
3. Stop and wait. When done, relay the report to the user in their language (default Vietnamese), concisely.

## Learning Mode (non-code topics / "kiểm tra kiến thức" + Web Fact-Check)
If the target is a study topic, a lesson, or Argon's own explanation (not code), the "tests" become a knowledge check. Argon does ALL of this itself:
- **L1 Web Fact-check (`search_web`):** list the key claims of the material; actively verify important or questionable claims using `search_web` and cite source URLs; mark ✔ confirmed / ✖ wrong (with correction) / ? uncertain. Flag unsafe or high-stakes advice (medicine, law, finance) and add caveats.
- **L2 Quiz:** write 8-12 questions mixing recall, understanding and application (multiple-choice, short answer, one scenario), with answer key and a one-line explanation each. Put the answer key in a separate section after the questions.
- **L3 Interactive check:** if the user answers, grade them, explain mistakes, and list weak topics to review.
- **L4 Report:** accuracy summary (with web sources), corrected claims, quiz, "Bài học/ôn tập" bullets, suggested next topics.
Never delete files; do not present uncertain claims as facts.

## Protocol given to Argon (paste into the prompt)

You are in TEST MODE. Target: <TARGET>. Work through these phases; do not skip any.

**P1 Discover** — Find how the project builds/tests/lints (package.json, pyproject, Makefile, CI config, existing tests). Note exact commands. Identify the code under test (use `git diff`/`git status` if target is "recent changes").

**P2 Baseline** — Run existing build + tests + lint. Record pass/fail. Do not fix unrelated pre-existing failures; list them separately.

**P3 Write tests** — For the target, add or extend tests covering: happy path, edge cases (empty, null, boundaries, large input), error paths, and any bug-prone logic. Match existing test style/framework. If no framework exists, use the lightest standard one (pytest / node:test / go test) or a runnable script with assertions. If the target is not code (docs, config, UI), write a concrete verification script/checklist and execute it.

**P4 Run & fix loop** (max 5 iterations):
 1. Run tests. 2. For each failure, decide: bug in code → fix minimal; bug in test → fix test. Never weaken or delete a valid assertion to get green. 3. Re-run the full suite (watch for regressions).
 If still failing after 5 iterations, or a fix needs a design decision → STOP, report BLOCKED with root-cause hypothesis.

**P5 Self-review checklist** — Re-read your own diff and confirm: no debug prints/TODOs left, no hard-coded secrets/paths, no unrelated changes, error handling present, naming/style consistent, public behavior unchanged unless requested. Run lint/type-check again. Fix findings and re-run tests.

**P6 Report** (compact, learning-oriented; write in the user's language, default Vietnamese):
- Status: PASS / PASS-WITH-NOTES / BLOCKED
- Commands used (build/test/lint) — and how the user can re-run them
- Baseline vs final results (counts)
- Tests added (file → what they cover), each with a one-line "why this case matters" (e.g. boundary, null, error path)
- Bugs found & fixed (one line each: cause → fix)
- "Bài học": 3-5 bullets on testing concepts illustrated by this run (e.g. edge cases, assertions, regression, why never weaken a test)
- Pre-existing/unfixed issues, assumptions, open questions

Also: write tests that are simple and readable, with a short comment above each group explaining what it checks, so the file itself teaches.

## Rules
- **NEVER delete files** (no rm/del/Remove-Item, no deleting directories, no `git clean`, no removing test files). If deletion seems needed, Argon STOPS and reports BLOCKED; the dispatcher must then ask the user to confirm **3 separate times** (3 sequential `ask_question` calls naming exact paths) and proceed only if all 3 are approved. Include this rule in every Argon prompt.
- Evidence over claims: only report PASS if the final test run actually passed in this session; include the command.
- Minimal diffs. No refactors outside the target.
- Never run destructive commands (rm -rf, git reset --hard, force push, DB drops) or touch files outside the project.
