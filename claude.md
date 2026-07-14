# A

## Access session from another directory

> Inside the picker, `Ctrl+W` widens to all worktrees of the repository and `Ctrl+A` widens to every project on this machine. And selecting a session from another worktree of the same repository resumes it in place, while selecting one from an unrelated project copies a `cd` and resume command to your clipboard instead.

> source: https://code.claude.com/docs/en/sessions

---

# B

## Branch conversation

`/branch [name]` creates a branch of the current conversation at the current point, so you can try a different direction without losing the conversation as it stands. It switches you into the new branch and preserves the original. The optional `name` just labels the branch so it's easier to find later. Think of it as a checkpoint-and-fork: everything up to that moment is copied, and you continue from a clean divergence point.

**Moving between branches**: Each branch is a separate session, so you navigate with `/resume`: `/resume [session]` resumes a conversation by ID or name, or opens the session picker. From the terminal before launching, the equivalents are `claude --resume <id>` or `claude -c` for the most recent session.

---

# M

## Move session from one directory to another

> From inside an active session you can run `/cd <target-dir>`, and as of Claude Code v2.1.196 moving a session with `/cd` relocates it to the new directory's project storage, so it appears in that directory's picker afterward. It also stays out of the old directory's picker cleanly.

> If you just want to reach a session from another directory rather than permanently relocate it, you have lighter options. Inside the picker, `Ctrl+W` widens to all worktrees of the repository and `Ctrl+A` widens to every project on this machine. And selecting a session from another worktree of the same repository resumes it in place, while selecting one from an unrelated project copies a `cd` and resume command to your clipboard instead.

> source: https://code.claude.com/docs/en/sessions

---

# U

## Update

```bash
sudo npm update -g @anthropic-ai/claude-code
```

> source: https://www.getaiperks.com/en/ai/how-to-update-claude-code
