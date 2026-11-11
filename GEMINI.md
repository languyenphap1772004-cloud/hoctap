# Global Safety Rules & Auto-Loaded Skills (apply to ALL sessions, agents and subagents)

## 1. Never delete files (MANDATORY)
- NEVER delete any file or directory, by any means: `rm`, `del`, `rmdir`, `Remove-Item`, `git clean`, `git rm`, `git reset --hard`, `git checkout -- <file>` that discards changes, emptying/overwriting a file so its content is lost, moving files to trash, or scripts that do any of these.
- This applies to the main agent, every subagent (including `argon`, `research`, `browser`, `self`), every skill and every swarm worker. Subagents must be given this rule in their prompt.
- If deletion is truly necessary, you MUST ask the user to confirm **3 separate times** (3 sequential `ask_question` calls, each naming the exact paths). Proceed only if ALL 3 are explicitly approved. If any answer is not a clear yes, do not delete.
- Subagents may not ask; they must stop, report BLOCKED, and let the main agent ask the user.
- Prefer non-destructive alternatives (rename, copy to a backup name, leave in place, or report to the user).

---

## 2. Custom Slash Commands for Web / Mobile (`/claude-argon` & `/argon-test`)
Because the Antigravity Web UI (`/remote`) slash menu does not list custom workspace skills in the visual autocomplete dropdown, the user will type these commands directly in their message:

- **`/claude-argon <task or topic>`** (or `claude-argon <task>` or `ca: <task>`):
  Immediately read `.agents/skills/claude-argon/SKILL.md` and execute the `/claude-argon` workflow (Brain plans/reviews; `argon` subagent on `Model: flash` executes/drafts; ask via `ask_question` before ever using `pro`; for learning topics use the Learning Flow with structured Vietnamese explanation, examples, summary table, quiz, and web search).

- **`/argon-test <target or topic>`** (or `argon-test <target>` or `at: <target>`):
  Immediately read `.agents/skills/argon-test/SKILL.md` and execute the `/argon-test` workflow (`argon` subagent on `Model: flash` runs the P1-P6 test loop for code or L1-L4 Web Fact-Check + Quiz for study topics; ask via `ask_question` before ever escalating to `pro`).
