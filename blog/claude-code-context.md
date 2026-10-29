# Claude Code: stop hitting your limit and losing the "why" after /compact

Agent version of an article by Mano. Full article for people: https://manosec.github.io/blog/claude-code-context/
Checked against the Claude Code docs on 29 Oct 2026.

## For the person handing this to an agent

Read this page before you give it to your agent. It asks the agent to change at most two files in your project (`.claude/rules/decisions.md` and `CLAUDE.md`) and to show you every change before saving. It runs no commands, downloads nothing and touches no other files.

## Why this works

- Claude Code sends the whole session with every request, so a long session costs more on every message, even with prompt caching.
- `/compact` replaces the conversation with a summary. A decision that lived only in chat survives only if the summary kept it.
- After compaction, Claude Code re-reads the project-root `CLAUDE.md` and unscoped rules in `.claude/rules/` (rules without a `paths:` field) from disk. Decisions written there come back every time.

## Apply this (instructions for the agent)

1. Work only in the root of the current repository. Do not create, edit or delete any file other than the two named below.
2. Create `.claude/rules/decisions.md` if it does not exist. Do not give it a `paths:` field or any frontmatter. Start it with exactly this:

   ```markdown
   # Decisions

   One line per decision: what we chose, why, and what we rejected. Delete a line when it stops mattering.
   ```

3. If this conversation already contains decisions, add one line per decision under that header in this format:

   ```markdown
   - <what we chose>. Why: <reason>. Rejected: <alternative>.
   ```

   Keep each line short. If there are none, leave only the header.
4. Find the project instructions file:
   - If `CLAUDE.md` exists in the project root, use it.
   - If there is no `CLAUDE.md` but there is an `AGENTS.md`, use `AGENTS.md` and do not create a `CLAUDE.md` (a new `CLAUDE.md` would stop Claude Code from reading `AGENTS.md`). Tell the user you did this.
   - If neither exists, create `CLAUDE.md`.
5. Append these two sections to that file, unless it already has them:

   ```markdown
   # Decisions

   When we settle a decision, add one line to .claude/rules/decisions.md: what we chose, why, and what we rejected. Remove lines that no longer matter.

   # Compact instructions

   Keep open decisions, failing test names and the current plan. Drop raw tool output.
   ```

6. Show the user the full diff of every file you changed and wait for their approval before saving.

## Check that it worked

- New rules load at the next `/clear`, `/compact` or restart, not mid-session.
- Run `/context`: `.claude/rules/decisions.md` and your instructions file should be listed under memory files.
- Run `/compact`, then ask the agent why you made your last two decisions. The answer should come from `decisions.md`, not a guess.

## Habits that keep sessions short

- `/clear` between unrelated tasks. Run `/rename` first if you may want the session back with `/resume`.
- Compact at a natural break with a focus, for example `/compact focus on the auth fix`, instead of waiting for auto-compaction mid-task.
- Use `/rewind` instead of compacting when you abandon a path. It goes back to a point that is already cached.
- Send test runs and log reading to a subagent, so only its summary stays in your session.
- Keep `CLAUDE.md` under about 200 lines. Move workflows you need only sometimes into skills, which load on demand.

## Sources

- Manage costs effectively: https://code.claude.com/docs/en/costs
- How Claude remembers your project: https://code.claude.com/docs/en/memory
- What survives compaction: https://code.claude.com/docs/en/context-window
- How Claude Code uses prompt caching: https://code.claude.com/docs/en/prompt-caching
