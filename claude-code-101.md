# 🤖 Claude Code: Complete Revision Notes

> 📝 Extended version of the Claude Code 101 notes, with the missing pieces added (commands, shortcuts, settings, skills, custom commands, CLI flags, headless mode, CI). Skim the ⭐ items for last-minute revision. Written from memory, so confirm exact flags and commands at `docs.claude.com` if something looks off.

---

## 1️⃣ Big Picture 🧭

```
💬 Claude.ai    →  chat; you copy-paste code
🤖 Claude Code  →  agent; reads, edits, runs commands itself
```

**Agentic loop:** `Prompt → Think → Use tool → Observe result → Repeat until done`

| Built-in tool | Purpose |
|---|---|
| 📖 Read / Glob / Grep | Explore files and search code |
| ✏️ Edit / Write | Change or create files |
| 🖥️ Bash | Run terminal commands |
| 🌐 WebSearch / WebFetch | Look up docs and APIs |
| 🧬 Task (subagents) | Delegate work to separate contexts |

⭐ **Three core constraints:** finite **context window**, **permissions** (it asks first), and **imperfection** (review its work).

---

## 2️⃣ Installation & Surfaces 📦

| Surface | Notes |
|---|---|
| 🖥️ Terminal | Newest features first. Run `claude` inside a project folder |
| 🔵 VS Code / 🟠 JetBrains | Native IDE integration with diff views |
| 🖱️ Desktop app | "Code" mode, good for async/background tasks |
| 🌐 Web `claude.ai/code` | Works on GitHub repos remotely |
| 📱 Mobile | Can monitor and delegate remotely |

- ⚠️ Package-manager installs (`brew`, `winget`) don't auto-update; the native installer does.
- 🔐 First run: `claude` → log in (`/login`). Check setup with `/doctor`.
- 📁 Access is scoped to the folder you launch from. Add more with `/add-dir`.

---

## 3️⃣ Slash Commands Cheat Sheet ⚡ ⭐

| Command | What it does |
|---|---|
| `/help` | List all commands |
| `/init` | Auto-generate a starter `CLAUDE.md` |
| `/clear` | Wipe context (starting a new task) |
| `/compact [focus]` | Summarize and keep going (mid-task) |
| `/context` | See what's using the context window |
| `/cost` / `/usage` | Token usage and cost / plan limits |
| `/model` | Switch model |
| `/memory` | Edit `CLAUDE.md` files |
| `/permissions` | View or edit allow/deny rules |
| `/mcp` | Manage MCP servers |
| `/agents` | Create and manage subagents |
| `/hooks` | Configure hooks |
| `/resume` | Pick up an earlier session |
| `/rewind` | Roll back conversation and/or code changes |
| `/config` | Open settings |
| `/status` | Show version, model, account |
| `/add-dir` | Grant access to extra directories |
| `/terminal-setup` | Fix newline and keybinding setup |

---

## 4️⃣ Keyboard Shortcuts & Input Tricks ⌨️ ⭐

| Input | Effect |
|---|---|
| `⇧ Tab` | Cycle modes: Approval → Auto-accept → Plan |
| `Esc` | Interrupt Claude mid-action |
| `Esc Esc` | Open rewind / jump back to an earlier message |
| `Ctrl+C` | Cancel current input or operation |
| `Ctrl+D` | Exit |
| `Ctrl+R` | Search prompt history |
| `@file` | Reference a file or folder in your prompt |
| `!command` | Run a bash command directly, output goes into context |
| `/` | Open the slash-command menu |
| `\` + Enter | New line in a prompt (or `⇧ Enter` once terminal is set up) |

---

## 5️⃣ Modes & Permissions 🔐

```
🟡 Default / Approval  →  asks before edits and commands
🟢 Auto-accept edits   →  edits auto-approved, commands still ask
🔵 Plan Mode           →  read-only research → proposes plan → you approve
```

**Permission rules** (in `settings.json`):

```json
{
  "permissions": {
    "allow": ["Bash(npm run test:*)", "Bash(git diff:*)"],
    "ask":   ["Bash(git push:*)"],
    "deny":  ["Read(./.env)", "Bash(rm -rf:*)"]
  }
}
```

- ⭐ **Precedence:** `deny` > `ask` > `allow`
- 🛡️ Deny rules are the right way to protect secrets (`.env`, keys).
- ☠️ `--dangerously-skip-permissions` removes all prompts. Only use it in sandboxes or containers.

---

## 6️⃣ Core Workflow 🔄

```
🔍 EXPLORE → 📋 PLAN → 💻 CODE → ✅ VERIFY → 🚀 COMMIT
```

**Habits that improve output:**
- 🎯 Be **specific** (file names, constraints, expected behavior)
- 🧪 Give a **way to verify**: tests, a linter, screenshots, a build command
- 🪜 Break large work into small steps and commit often
- 🔁 Same mistake twice? Add a rule to `CLAUDE.md`
- 🧹 `/clear` between unrelated tasks
- ⏪ Claude went wrong? `Esc Esc` or `/rewind`. Don't argue with a derailed context.

**Prompt shape that works:**
```
Goal:        what you want
Context:     where it lives (@files)
Constraints: style, libs, don't-touch areas
Done when:   tests pass / behavior X works
```

---

## 7️⃣ Context Management 🧠 ⭐

| Action | When |
|---|---|
| `/compact` | Same task, context getting full |
| `/clear` | New, unrelated task |
| `/context` | Diagnose what's eating tokens |
| Subagents | Heavy lookups where only the answer matters |

**Saving context:**
- Specific prompts mean less blind exploration
- Disable unused MCP servers and skills
- Don't paste huge logs; reference files or have Claude grep
- Auto-compact triggers near the limit, but manual `/compact` with a focus note is better: `/compact keep the DB schema decisions`

---

## 8️⃣ `CLAUDE.md`: Persistent Instructions 🧾 ⭐

**Loaded automatically at session start.** It's an onboarding doc, not a manual.

| Location | Scope |
|---|---|
| `~/.claude/CLAUDE.md` | 👤 You, all projects |
| `./CLAUDE.md` | 📁 Project, commit it for the team |
| `./CLAUDE.local.md` | 🔒 Personal project notes (gitignore) |
| Subfolder `CLAUDE.md` | 📂 Loaded when Claude works in that folder |

**What belongs in it:**
- Build/test/lint commands
- Code style and architecture conventions
- Gotchas and "never do X" rules
- Imports: `@docs/api.md`, `@README.md`

**Keep it lean.** Long files dilute attention. Prune regularly.

⚠️ `CLAUDE.md` is **guidance** (usually followed). A **hook** is a **guarantee**.

---

## 9️⃣ Settings Hierarchy ⚙️

```
Enterprise managed policy   (highest priority, can't be overridden)
        ↓
CLI flags
        ↓
.claude/settings.local.json   (personal, project, gitignored)
        ↓
.claude/settings.json         (shared with team, committed)
        ↓
~/.claude/settings.json       (user-wide defaults)
```

Settings hold: permissions, hooks, env vars, model default, MCP config.

---

## 🔟 Custom Slash Commands 🧩

Reusable prompts stored as Markdown files.

```
.claude/commands/review.md      →  /review        (project)
~/.claude/commands/explain.md   →  /explain       (personal)
```

```markdown
---
description: Review staged changes
---
Review the staged git changes for bugs and style issues.
Focus area: $ARGUMENTS
```

Usage: `/review security` → `$ARGUMENTS` = `security`

---

## 1️⃣1️⃣ Skills 🎁 ⭐

Folders of instructions (plus optional scripts and resources) that Claude loads **only when relevant**.

```
.claude/skills/pdf-report/
├── SKILL.md        ← frontmatter + instructions
├── template.md
└── scripts/make.py
```

```markdown
---
name: pdf-report
description: Generate branded PDF reports. Use when the user asks for a report or PDF export.
---
Instructions here...
```

| Concept | Detail |
|---|---|
| 🔍 Discovery | Claude sees only `name` + `description` up front |
| 📖 Loading | Full body loads **when the task matches** (progressive disclosure) |
| ✍️ Key skill | Write a **precise description**, since that's what triggers it |
| 📁 Locations | `~/.claude/skills/` (personal), `.claude/skills/` (project) |

**Skill vs Command vs CLAUDE.md**

| | Loads when | Best for |
|---|---|---|
| `CLAUDE.md` | Always | Universal project rules |
| Skill | Auto, when relevant | Task-specific expertise |
| Slash command | You type it | Repeatable prompts you trigger manually |

---

## 1️⃣2️⃣ Subagents 🧬

```
Main agent 🤖 ──▶ 🔬 Subagent (own context, own tools) ──▶ 📄 summary back
```

```markdown
---
name: code-reviewer
description: Reviews code for bugs and style. Use after code changes.
tools: Read, Grep, Glob
model: sonnet
---
You are a careful reviewer. Report issues by severity...
```

- 📁 `.claude/agents/` (project) or `~/.claude/agents/` (personal)
- 🛠️ Restrict `tools` (read-only reviewer = safer)
- 🧠 Benefit: **isolated context**, so the main window stays clean
- ⚡ Can run in parallel for independent research tasks
- 💡 Good uses: code review, test-writing, codebase exploration, security audit

---

## 1️⃣3️⃣ MCP: Model Context Protocol 🔌

```
Claude Code ──▶ MCP Server ──▶ External system (GitHub, Linear, DBs, Figma...)
```

```bash
# Remote HTTP server
claude mcp add --transport http linear https://mcp.linear.app/mcp

# Local stdio server
claude mcp add context7 -- npx -y @upstash/context7-mcp

claude mcp list
claude mcp remove <name>
```

| Scope flag | Stored | Shared? |
|---|---|---|
| `--scope local` (default) | Your private project config | ❌ |
| `--scope project` | `.mcp.json` in repo | ✅ team |
| `--scope user` | All your projects | ❌ |

- 💸 Each connected server adds tool definitions to context. Trim with `/mcp`.
- 💡 A CLI tool (`gh`, `aws`) or a Skill is often cheaper than an MCP server.
- ⚠️ Only add servers you trust, because they can feed content into Claude's context.

---

## 1️⃣4️⃣ Hooks 🪝 ⭐

**Deterministic shell commands that run at lifecycle events.** They always fire.

| Event | Fires |
|---|---|
| `PreToolUse` | Before a tool runs (**can block**) |
| `PostToolUse` | After a tool succeeds |
| `UserPromptSubmit` | When you submit a prompt |
| `Stop` | When Claude finishes responding |
| `SubagentStop` | When a subagent finishes |
| `Notification` | When Claude needs your attention |
| `PreCompact` | Before context compaction |
| `SessionStart` / `SessionEnd` | Session begins and ends |

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|MultiEdit|Write",
        "hooks": [
          { "type": "command", "command": "npx prettier --write \"$CLAUDE_PROJECT_DIR\"" }
        ]
      }
    ]
  }
}
```

| Exit code | Meaning |
|---|---|
| `0` | ✅ Success / proceed |
| `2` | ⛔ Block. `stderr` is fed back to Claude |
| other | ⚠️ Non-blocking error, shown to you |

- Hook receives **JSON on stdin** (tool name, inputs, etc.)
- `matcher` = which tools it applies to
- 📁 Project hooks live in `.claude/settings.json`, so the team shares them

**Uses:** auto-format, run tests after edits, block edits to protected files, log for compliance, desktop notifications.

---

## 1️⃣5️⃣ Git & GitHub Workflows 🌿

| Feature | Detail |
|---|---|
| Commit messages | Ask Claude to write them from the diff |
| `/commit-push-pr` style skill | One step: commit → push → PR |
| `claude --from-pr <NUM>` | Resume a session tied to a PR |
| 👀 Reviewer subagent | Fresh-eyes review before pushing |
| GitHub integration | `/install-github-app`, then tag `@claude` in issues and PRs |
| 🧪 Parallel work | Use **git worktrees** to run multiple Claude sessions on separate branches |

---

## 1️⃣6️⃣ Headless Mode & CLI Flags 🖥️ ⭐

```bash
claude                          # interactive session
claude "explain this repo"      # start with a prompt
claude -p "list TODOs"          # print mode: one-shot, then exit (scripts/CI)
cat error.log | claude -p "find root cause"   # pipe input
claude -c                       # continue most recent session
claude -r                       # pick a session to resume
```

| Flag | Purpose |
|---|---|
| `-p, --print` | Non-interactive output |
| `--output-format json` / `stream-json` | Machine-readable output |
| `--model` | Choose model for this run |
| `--permission-mode plan` | Start in Plan Mode |
| `--allowedTools` / `--disallowedTools` | Pre-approve or block tools |
| `--add-dir` | Extra directories |
| `--append-system-prompt` | Add instructions to the system prompt |
| `--max-turns` | Limit agent iterations |

🧰 **Claude Agent SDK**: build your own agents using the same engine (Python / TypeScript).

---

## 1️⃣7️⃣ Safety & Good Practice 🛡️

- 👀 **Read diffs** before accepting. You own the code.
- 🔑 Never put secrets in prompts or `CLAUDE.md`; deny-read `.env`.
- 🧪 Tests + version control = your safety net. Commit before big changes.
- 🧱 Prefer Plan Mode for anything touching many files.
- 📦 Treat third-party MCP servers and downloaded skills like third-party code: review them first.
- 🔄 Long, messy session? Start fresh with a clear summary rather than fighting it.

---

## 🧪 Quick Revision Q&A

| ❓ Question | ✅ Answer |
|---|---|
| Difference between `/clear` and `/compact`? | Clear wipes everything. Compact summarizes and continues. |
| Which loads always vs on demand? | `CLAUDE.md` always. Skills when relevant. Commands when you type them. |
| Guaranteed behavior, prompt or hook? | **Hook.** |
| What does exit code 2 do in a hook? | Blocks the action and sends stderr to Claude. |
| Why use a subagent? | Separate context window. Only a summary returns. |
| Where do team-shared MCP servers go? | `.mcp.json` (project scope). |
| Rule precedence in permissions? | deny > ask > allow |
| How to run Claude in a CI script? | `claude -p "..."` (add `--output-format json` if needed) |
| How to undo a bad turn? | `Esc Esc` or `/rewind` |
| What triggers a skill? | Its `description` matching the task |
| How to reduce MCP context cost? | Disable unused servers via `/mcp`, or use a CLI or skill instead |
| Best mode for complex multi-file change? | Plan Mode (`⇧ Tab`) |

---

## 🎯 Final Takeaways

1. 🔄 **Explore → Plan → Code → Verify → Commit** is the highest-leverage habit.
2. 🧠 **Context is finite.** Spend it deliberately (`/compact`, `/clear`, subagents, trimmed MCP).
3. 🧾 **Instruct at the right layer:** `CLAUDE.md` for always-on rules, skills for specialized know-how, commands for manual shortcuts, hooks for guarantees.
4. 🔐 **You stay in control** through permissions, Plan Mode, rewind, and code review.
5. 🤝 **Scale up** with subagents, MCP, headless mode, and GitHub integration.