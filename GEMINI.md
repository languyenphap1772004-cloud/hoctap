# Global Safety Rules (apply to ALL Antigravity sessions, agents and subagents)

## Never delete files
- NEVER delete any file or directory, by any means: `rm`, `del`, `rmdir`, `Remove-Item`, `git clean`, `git rm`, `git reset --hard`, `git checkout -- <file>` that discards changes, emptying/overwriting a file so its content is lost, moving files to trash, or scripts that do any of these.
- This applies to the main agent, every subagent (including `argon`, `research`, `browser`, `self`), every skill and every swarm worker. Subagents must be given this rule in their prompt.
- If deletion is truly necessary, you MUST ask the user to confirm **3 separate times** (3 sequential `ask_question` calls, each naming the exact paths). Proceed only if ALL 3 are explicitly approved. If any answer is not a clear yes, do not delete.
- Subagents may not ask; they must stop, report BLOCKED, and let the main agent ask the user.
- Prefer non-destructive alternatives (rename, copy to a backup name, leave in place, or report to the user).
