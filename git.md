# A

## Add everything except untracked files

If you need to `add` everything except untracked files, i.e. only update changes to files that are already tracked

```bash
git add -u # --update
```

---

# C

## Configuration of keys for different repos

```bash
$ cat .ssh/config
```

```bash
# Check the Host line
# Then the remote origin url should be modified from, e.g.
# git@github.com:epfl-lasa/robetarme_ros2_wp5.2.git
# to
# git@github.com-robetarme-user:epfl-lasa/robetarme_ros2_wp5.2.git
Host github.com-robetarme-user
  HostName github.com
  User git
  IdentityFile ~/.ssh/robetarme_passphraseless_key
  IdentitiesOnly yes

# Default identity
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_iti581_github
  IdentitiesOnly yes
```

## `cherry-pick` a commit

### The two branches are in the same repository

Let the commit in question have hash `abc123`. Then

```bash
git checkout <branch-a>
git cherry-pick abc123
```

### The two branches are NOT in the same repository

Two ways to do this.

#### Option 1: Add the source repo as a remote

```bash
cd /path/to/target-repo
git remote add other /path/to/source-repo
git fetch other
git cherry-pick <commit-hash>
git remote remove other   # optional cleanup
```

#### Option 2: Export a patch and apply it

```bash
# In the source repo
git format-patch -1 <commit-hash>   # creates 0001-xxx.patch

# In the target repo
git am /path/to/0001-xxx.patch
```

If the patch doesn't apply cleanly, you can try `git am -3` (three-way merge) or fall back to `git apply --reject` and fix things manually.

---

# D

## `diff` different files across different branches

```bash
git diff main:src/file_a.txt feature:src/folder/file_b.txt
```

---

# F

## Fetch by specifying key location

```bash
GIT_SSH_COMMAND='ssh -i /path/to/private_key' git fetch origin
```

## Find files changed between commits filtered by regex

Say you want to get all yaml files which changed between two commits

```bash
git diff --name-only <commit-sha-1> <commit-sha-2> -- '**/*.yaml'
```

If you want to diff them

```bash
commit_sha1=<commit-sha-1>
commit_sha2=<commit-sha-2>
files=$(git diff --name-only $commit_sha1 $commit_sha2 -- '**/*.yaml')
for file in $files; do git difftool $commit_sha1 $commit_sha2 -- "$file"; done
```

## Find unstaged files in which a particular string was modified

Say string `KeyError` was modified in a bunch of files that you haven't yet committed. Find these files with

```bash
git diff HEAD -S KeyError --name-only
```

Search for a regex with `-G` instead

```bash
git diff HEAD -G KeyError* --name-only
```

## Forgot to add file(s) to latest commit and realised before pushing?

Don't worry. You can add the file(s) to the latest commit with

```bash
git add <forgotten-files>
git commit --amend
```

---

# H

## Hooks

### Pre-commit

#### Install

```bash
pre-commit install
```

#### Run without committing

```bash
pre-commit run -a
```

---

# L

## LFS

### Installation

```bash
git lfs install
```

### Add stuff to

```bash
git lfs track "file-larger-than-100MB.psd"
```

or place files names and regexes in `$REPOSITORY_PATH/.gitattributes`

### Migrate existing files to LFS (e.g. large files already committed)

```bash
git lfs migrate import --include="*.bin,*.zip" --everything
```

### Remove commit-unreferenced stuff from

1. Delete files
2. Commit
3. `bash git lfs prune`

## List files modified or yet untracked

```bash
git status --porcelain | awk '{print $2}'
```

If you add the following to `~/.gitconfig` then issuing `git changed-files` will constitute an abbreviation

```gitignore
[alias]
    changed-files = "!git status --porcelain | awk '{print $2}'"
```

---

# M

## Merge branch but don't commit changes yet

Useful when you need to merge changes but not incorporate all of them

```bash
# Switch to target branch (e.g. master)
git checkout target-branch

# Merge but don't commit yet. The use of --no-ff is essential
git merge --no-commit --no-ff source-branch

# Reset a specific file to its state before merge
git checkout HEAD -- path/to/file.txt

# Now commit the merge
git commit -m "Merge source-branch, excluding changes to file.txt"
```

Unstage everything

```bash
git reset
```

Then you may keep the files that you need and discard the rest.

## Modify commit message

Assume you want to modify the message or the description of commit `<commit-sha>`. Use

```bash
git rebase -i <commit-sha>~1
```

and mark the commit you want to change with `reword` or `r`. Close the editor; a new editor with the commit message and description will open.

## Modify commit content

Assume you want to modify the content of commit `<commit-sha>`. Use

```bash
git rebase -i <commit-sha>~1
```

and mark the commit you want to change with `edit` or `e`. Close the editor. Then modify the files you want. Then

1. `git add <modified-files>`
2. git commit --amend
3. git rebase --continue

---

# P

## Push by specifying key location

```bash
GIT_SSH_COMMAND='ssh -i /path/to/private_key' git push origin main
```

## Force-push but prevent accidental overwrite

> `--force-with-lease` is safer than `--force` as it will refuse to push if someone else has pushed to the branch in the meantime, thus preventing you from accidentally overwriting their work

---

# R

## Rebase and automatically accept changes from branch

### TL;DR

#### variant A

You are on branch `master` and you want to rebase `origin/master` on top of it. You can keep the version of `origin/master` instead of `master` with

```bash
# On branch master
# git rebase origin/master
git checkout --ours -- .
```

#### variant B

You are on branch `branch` and you want to rebase your work on `master`. You can keep the version of `branch` instead of `master` with

```bash
# On branch branch
# git rebase master
git checkout --theirs -- .
```

### Long story

It is possible to resolve `rebase` conflicts automatically by favouring the changes from branch `b` (the branch being rebased) over those from `master` (the base branch, the branch onto which `b` is rebased). However, Git doesn’t do this by default; you need to explicitly instruct it. During a `rebase` with conflicts, Git pauses and asks you to resolve them. If you want to automatically accept all changes from branch `b` (i.e., `ours` in the context of a `rebase`) you can use:

```bash
git checkout --theirs -- .
```

> [!CAUTION]
> During a rebase the meanings of `ours` and `theirs` are reversed compared to a merge:
>
> - `ours` refers to the incoming base (i.e., `master`, the branch you're rebasing onto).
> - `theirs` refers to the current commit from branch `b` (the one being replayed).

After running the above command in the conflicted directory (or for specific files), stage the resolved files:

```bash
git add .
```

Then continue the rebase:

```bash
git rebase --continue
```

If you want to do this non-interactively for the entire `rebase`, you can pass a strategy option before the `rebase` starts:

```bash
git rebase -X theirs master
```

This tells Git to automatically prefer the changes from branch `b` (the `theirs` side during `rebase`) whenever conflicts occur.

> [!CAUTION]
> Automatically discarding changes from `master` may unintentionally remove important updates.

## Remove changes introduced by commit

### Create a New Commit That Undoes It (`git revert`)

```bash
git revert <commit-hash>
```

### Remove from history

With interactive rebase

```bash
git rebase -i <base-branch-or-commit>
```

Locate the commit you want to remove in the editor. Then change the word `pick` to `drop`. Save and close the editor. If rebasing requires resolving conflicts then resolve them and continue the rebase with

```bash
git rebase --continue
```

## Resolve conflicts after merge

>[!NOTE]
> You might want to have meld configured as your `difftool` as well as your `mergetool`
> ```
> git config --global merge.tool meld
> ```

```bash
git mergetool <conflicted-file>
```
Meld will open with three panes: your branch's version on the left, the other branch's version on the right, and the merge result in the middle. Edit the middle pane, save, and close meld. Git will then mark the file as resolved and ask whether to keep the backup .orig file. After that, finish the merge with:

```bash
git add <conflicted-file>   # usually automatic after mergetool, but check git status
git commit
```

## Restore file to past state using commit hash

```bash
git checkout <commit-hash> -- <filename>
```

## Revert multiple commits in one commit

### Commits are sequential

```bash
git revert --no-commit OLDEST_COMMIT_HASH^..NEWEST_COMMIT_HASH

git commit -m "Revert multiple commits: description of changes"
```

### Commits are scattered

```bash
# Revert specific commits (in reverse chronological order - latest first)
git revert --no-commit COMMIT_HASH_n COMMIT_HASH_n-1 ... COMMIT_HASH_n-k

git commit -m "Revert specific commits"
```

---

# S

## Squash one branch to a single commit; then place it in a branch

Suppose that in a github repository there are two branches, branch B and branch C. Branch B has a bunch of commits that should be squashed into one commit, and then placed in branch C.

```bash
git checkout C

# This "teleports" the files from branch B into the current working directory and automatically stages them, but it does not bring over the commit history.
git checkout B
```

If not all files from branch B should be copied but only those under a specific directory, then you may achieve it like so:

```bash
git checkout C
git checkout B -- path/to/directory
```

## Squash a sequence of commits to a single commit; then place it in a branch

Suppose that in a github repository there are two branches, branch B and branch C. Branch B has a bunch of commits, a finite sequence of which should be squashed into one commit, and then placed in branch C.

Start by creating a temporary branch that points to the last commit in the sequence you want from branch B.

```bash
git checkout -b temp-slice <last-commit-hash>
```

Perform a "soft reset" back to the commit **immediately before the start of the desired sequence**. This collapses all changes in the sequence into the staging area.


```bash
# <start-commit-hash-1> is the hash of the commit **just before** the sequence starts
git reset --soft <start-commit-hash-1>
```

```bash
git commit -m "Squashed sequence from branch B"
```

Now follow [Squash a sequence of commits to a single commit; then place it in a branch](#squash-one-branch-to-a-single-commit-then-place-it-in-a-branch), where the new branch B is `temp-slice`

## Squash local commits before pushing

You've made N incremental local commits but you want to squash them into one commit before pushing it.

```bash
git rebase -i HEAD~N
```

An editor will open with a list like:

```bash
pick 123abc Commit message 1
pick 456def Commit message 2
pick 789ghi Commit message 3
...
pick 012jkl Commit message N
```

Change all but the first entries to `squash`

```bash
pick   123abc Commit message 1
squash 456def Commit message 2
squash 789ghi Commit message 3
...
squash 012jkl Commit message N
```

Save and close the editor. Git will then prompt you to edit the new commit message. You can combine or edit the messages as needed. Then save and close the editor again. After that the commits will be squashed into one.

## Squash commits during merge

You need to merge a branch onto `master` but you want to squash all commits from `b` into one commit.

```bash
git checkout master
git merge --squash <branch>
```

This does not create a merge commit. Instead it stages all the changes from `<branch>` into the index. Commit the squashed changes with

```bash
git commit -m "A single commit message summarizing all changes between <branch> and master"
```

## Squash commits during rebase

You have multiple commits on one branch that you need to rebase onto `master`, and there are potential conflicts with each commit. You want to avoid resolving multiple conflicts per commit and instead resolve the potential conflicts of one commit (if there are any after squashing them). Let the branch be `my-branch`. If you prefer not to rewrite `my-branch`'s history then

- Create a new branch from `master` and switch to it

```bash
git checkout master
git checkout -b my-branch-squashed
```

Apply the net changes from `my-branch` as one commit:

```bash
git merge --squash my-branch
git commit -m "Squashed all changes from my-branch"
```

You can now replace `my-branch` with `my-branch-squashed`

```bash
# On my-branch-squashed still
git rebase master
```

If you prefer to rewrite history then do an interactive rebase on `my-branch` first:

Checkout `my-branch`

```bash
git checkout my-branch
```

Squash all commits in `my-branch` into one (relative to `master`)

```bash
git rebase -i master
```

This opens an interactive editor listing all commits in `my-branch` that are not in master. In the editor:

- Keep the first commit as pick.
- Change all subsequent commits to squash (or s).
- Save and close.

Git will then prompt you to edit the commit message for the squashed commit. Finalize it and save. Now `my-branch` has a single commit on top of master. If you want to update master with this change:

```bash
git checkout master
git merge --ff-only `my-branch`
```

## Stash subset of staged files

```bash
git stash push path/to/file1 path/to/file2
```

---

# T

## Take back last commit but keep changes unstaged for further processing

> (commits) `A` <-- `B`. How to go back to A but keep B's changes as unstaged?

Surprisingly simple:

```bash
git reset HEAD~1
```

Use `--force-with-lease` if B was pushed.

---

# U

## Unstage everything after `add`

```bash
git reset
```

---

# V

## View file modifications

### Introduced in specific commit

```bash
git show <commit-hash> -- <file>
```

### Introduced by all affecting commits

```bash
git log -p --follow <file>

# List commits only
# git log --follow <file>
```

### When file is only staged

```bash
git difftool --cached <file>
```

### Side by side in command line

```bash
git diff --word-diff
```

## View ignored files

```bash
git ls-files --others --ignored --exclude-standard
```

---

# W

## Worktrees

> You are in the middle of working and your boss comes in and demands that you fix something immediately. You might typically use `git stash` to store your changes away temporarily, however, you don’t want to risk disturbing any of it. Instead, you create a temporary linked worktree to make the emergency fix, remove it when done, and then resume your work.

```
git worktree add -b emergency-fix ../temp master
pushd ../temp
# ... hack hack hack ...
git commit -a -m 'emergency fix'
popd
git worktree remove ../temp
```

> source: https://git-scm.com/docs/git-worktree#_examples
