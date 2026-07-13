# A

## Access session from another directory

> Inside the picker, `Ctrl+W` widens to all worktrees of the repository and `Ctrl+A` widens to every project on this machine. And selecting a session from another worktree of the same repository resumes it in place, while selecting one from an unrelated project copies a `cd` and resume command to your clipboard instead.

> source: https://code.claude.com/docs/en/sessions

---

# F

## Fork session

Resuming a session with `claude --continue` or `claude --resume` reopens it under the same session ID and appends new messages to the existing conversation. Forking with `--fork-session` or `/branch` copies the history into a new session ID, leaving the original unchanged.

> source: https://code.claude.com/docs/en/how-claude-code-works#resume-or-fork-sessions

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

> source: https://www.getaiperks.com/en/ai/how-to-update-claude-code#method-1-npm-update-recommended:~:text=Method%201%3A%20NPM%20Update%20(Recommended)
