# Agent = model + harness: audit the harness your agent runs in

Agent version of an article by Mano. Full article for people: https://manosec.github.io/blog/coding-agent-harness/
Checked against the Claude Code settings docs and the Codex source on 30 Oct 2026.

## For the person handing this to an agent

Read this page before you give it to your agent. It asks the agent to read its own settings files and write you a short report. It changes no files, runs no shell commands, downloads nothing and never prints secret values such as API keys or tokens.

## Why this works

- An agent is a model plus a harness. A source-code study of 11 coding agents (arXiv 2609.00006) found the same seven parts in all of them: the loop, the request layer and prompt cache, tools, context management, safety, sub-agents, and extensions such as hooks, skills and plugins.
- Benchmark scores barely track harness size. The 100-line Mini-SWE-Agent reports SWE-bench scores in the same range as Codex's 1.1 million lines of Rust. The extra code decides what the agent costs, what it runs without asking and how it isolates commands.
- Most of those decisions sit in plain settings files, so the agent can read them and tell you what they allow.

## Apply this (instructions for the agent)

1. Do not create, edit or delete any file. Do not run shell commands. Only read the files named below with your file-reading tool. Never print a secret value (API key, token, password); write "a secret is set" instead.
2. Name the coding agent you are running in, then read its settings:
   - Claude Code: `~/.claude/settings.json`, and in the project `.claude/settings.json`, `.claude/settings.local.json` and `.mcp.json`, plus `CLAUDE.md` in the project root and in `~/.claude/`.
   - Codex: `~/.codex/config.toml` and the `AGENTS.md` files in the project.
   - Any other agent: its documented settings files. Name the files you used.
   Skip files that don't exist and list them as missing.
3. Report on each part in one or two plain sentences:
   - Safety: what runs without asking (Claude Code: `permissions.allow` and `permissions.defaultMode`; Codex: `approval_policy`), what is blocked (Claude Code: `permissions.deny`), and whether commands run in a sandbox and what it can write to (Claude Code: the `sandbox` settings, if any; Codex: `sandbox_mode`).
   - Context: the size of every instruction file that loads in every session, in lines and characters.
   - Tools: every MCP server configured, since each one can add tools the model sees.
   - Extensions: every hook, with the event it runs on and the command it runs, plus any skills or plugins you can see.
   - Sub-agents: any custom sub-agents defined.
   - Cache: anything that switches the model or the tool set mid-session, since the first request after a switch re-reads the whole history uncached.
4. End with the three settings you would tighten first and why. Do not apply them. Wait for the user to decide.

## Check that it worked

- The report names every file it read and every file that was missing.
- Each rule it quotes matches what you see when you open the same file yourself.
- Nothing changed: `git status` in the project shows no new changes from the agent.

## Sources

- Harness Engineering: Anatomy, Architecture, and Evolution of Coding Agents (arXiv 2609.00006): https://arxiv.org/abs/2609.00006
- OpenAI, Unrolling the Codex agent loop: https://openai.com/index/unrolling-the-codex-agent-loop
- Claude Code settings files and precedence: https://code.claude.com/docs/en/settings
- Codex configuration reference: https://developers.openai.com/codex/config-reference
