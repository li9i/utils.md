# Setup

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

---

# `add`

## Add everything except untracked files

If you need to `add` everything except untracked files, i.e. only update changes to files that are already tracked

```bash
git add -u # --update
```

## Forgot to add file(s) to latest commit and realised before pushing?

Don't worry. You can add the file(s) to the latest commit with

```bash
git add <forgotten-files>
git commit --amend
```

---

# `checkout`

## Checkout as means of cherry-picking file from another branch

The following copies a file that originally resides to branch `master` to the `my-branch` branch.

```bash
# git checkout master  # on branch master
git checkout -b my-branch
git checkout master -- <filename>
```

## Checkout as means of restoring file to past state using commit hash

```bash
git checkout <commit-hash> -- <filename>
```

> [!NOTE]
> If you don't supply `commit-hash` then the file is restored to its staged version in the index. That matches the last commit only when the file has no staged changes.

---

# `cherry-pick`

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

# `diff`

## `diff` different files across different branches

```bash
git diff main:src/file_a.txt feature:src/folder/file_b.txt
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
git diff HEAD -G 'KeyError' --name-only
```

---

# `fetch`

## Fetch by specifying key location

```bash
GIT_SSH_COMMAND='ssh -i /path/to/private_key' git fetch origin
```

---

# Hooks (`pre-commit`)

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

# `lfs`

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
3. `git lfs prune`

---

# `merge`

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

---

# `push`

## Push by specifying key location

```bash
GIT_SSH_COMMAND='ssh -i /path/to/private_key' git push origin main
```

## Force-push but prevent accidental overwrite

> `--force-with-lease` is safer than `--force` as it will refuse to push if someone else has pushed to the branch in the meantime, thus preventing you from accidentally overwriting their work

---

# `rebase`

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

It is possible to resolve `rebase` conflicts automatically by favouring the changes from branch `b` (the branch being rebased) over those from `master` (the base branch, the branch onto which `b` is rebased). However, Git doesn’t do this by default; you need to explicitly instruct it. During a `rebase` with conflicts, Git pauses and asks you to resolve them. If you want to automatically accept all changes from branch `b` (i.e., `theirs` in the context of a `rebase`) you can use:

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

```bash
# 1. git add <modified-files>
# 2. git commit --amend
# 3. git rebase --continue
```

## `--onto`

`git rebase --onto` lets you replay a specific *range* of commits onto a new base, decoupling "what to move" from "where to put it." Regular rebase always assumes those are the same point; `--onto` splits them.

The form:

```
git rebase --onto <newbase> <upstream> [<branch>]
```

Mental model: **take the commits in `<upstream>..<branch>` (upstream exclusive) and plant them on `<newbase>`.**

- `<newbase>` — where the commits land
- `<upstream>` — the *old* base; commits after this point are what get moved
- `<branch>` — the tip of the range (defaults to current HEAD, and gets checked out/moved)

Two canonical uses:

**Re-point a branch.** You branched `feature` off `main` but meant to branch it off `develop`:

```
git rebase --onto develop main feature
```

Moves everything in `main..feature` onto `develop`.

**Drop a lower branch in a stack.** Say the bottom PR (`feature-a`) squash-merged into `main`, so its commits now exist in a different form. You want `feature-b` re-pointed onto `main` *without* replaying `feature-a`'s originals:

```
git rebase --onto main feature-a feature-b
```

This takes only the `feature-a..feature-b` commits and lands them on the updated `main`, cleanly dropping the now-redundant lower commits — the case `--update-refs` alone doesn't handle, since a squash merge breaks the ancestry it relies on.

You can also drop a middle range by pointing `--onto` at the commit just before the bad range and using the last bad commit as `<upstream>`.

---

# `reflog`

## Reflog

The reflog is a per-repository, purely local log of where your refs have *been*. Every time `HEAD` or a branch tip moves—commit, reset, rebase, merge, checkout, cherry-pick, amend—Git records the old SHA, the new SHA, and a one-line note about what caused the move. It is never fetched, pushed, or shared: two people on the same repo have entirely different reflogs, and a fresh `git clone` starts with an empty one.

The mental model: branches and `HEAD` are just pointers to commits, and almost every Git operation is really "move a pointer". The reflog is the append-only history of those moves. So a `reset --hard` never deletes anything—it moves a pointer, and the reflog still holds the SHA it used to point at. That is what makes the operations below recoverable.

Show `HEAD`'s reflog (most operations):

```bash
git reflog
```

Show a single branch's reflog (cleaner when tracing one branch):

```bash
git reflog show <branch>
```

Reference syntax: `HEAD@{n}` is "n moves ago for `HEAD`"; `<branch>@{n}` is the same per-branch. A time-based form also works when you remember *when* rather than how many operations ago:

```
git reset --hard HEAD@{2.hours.ago}
git log main@{yesterday}
```

### Undo the last history-changing operation

`HEAD@{1}` is almost always wherever you were before the thing you just regretted (a bad `reset`, `rebase`, `merge`, or `commit --amend`).

```bash
git reset --hard HEAD@{1}
```

> [!CAUTION]
> A `reset` itself creates a new reflog entry, so after one undo the old `HEAD@{1}` becomes `HEAD@{2}`. Don't count indices while the list shifts under you—read the reflog, find the SHA of the good state, and reset to the **SHA**, not the `@{n}`.

### Recover from a botched rebase

Every rebase brackets its work with `rebase (start)` and `rebase (finish)` entries. To throw away a bad rebase, reset to whatever HEAD pointed at immediately before its `rebase (start)`. Since the reflog prints newest-first, that is the entry directly below the `rebase (start)` line. It holds the pre-rebase state and can be any kind of move: a `commit`, a `checkout`, or the `rebase (finish)` of an earlier rebase.

```bash
git reflog
# ...
# 8d662d24 HEAD@{5}: rebase (start): checkout HEAD~3    <- the bad rebase begins here
# 48e8a195 HEAD@{6}: commit: add input validation       <- HEAD just before the rebase; reset here
# ...
git reset --hard 48e8a195
```

Then redo the rebase correctly. This is the safety net that makes aggressive rebasing safe: the pre-rewrite state is always one reflog lookup away.

### Recover a lost commit or a branch deleted with `-D`

A commit orphaned by a rewrite, or a branch deleted with `git branch -D`, isn't gone—it's just unreferenced. Find its SHA in the reflog and point a fresh branch at it.

```
git reflog                      # or: git log -g (same data, full-log format, greppable)
git branch recovered <sha>
```

`git log -g --grep=<text>` is handy for hunting a specific commit message across the reflog.

### Inspect what a ref pointed at, at a past state

```
git show HEAD@{3}           # the commit HEAD pointed at 3 moves ago
git diff main@{1} main      # what changed on main in its last move (e.g. after a pull)
```

> [!NOTE]
> Reflog entries expire: reachable-commit entries after 90 days (`gc.reflogExpire`), unreachable ones after 30 (`gc.reflogExpireUnreachable`). It rescues recent mistakes, not one from last quarter. It also tracks only committed history—uncommitted changes destroyed by `reset --hard` are genuinely gone (that is what `git stash` is for).

---

# `reset`

## Take back last commit but keep changes unstaged for further processing

> (commits) `A` <-- `B`. How to go back to A but keep B's changes as unstaged?

Surprisingly simple:

```bash
git reset HEAD~1
```

Use `--force-with-lease` if B was pushed.

## Unstage everything after `add`

```bash
git reset
```

---

# `revert`

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

# `squash`

## Squash one branch to a single commit; then place it in a branch

Suppose that in a github repository there are two branches, branch B and branch C. Branch B has a bunch of commits that should be squashed into one commit, and then placed in branch C.

```bash
git checkout C

# This "teleports" the files from branch B into the current working directory and automatically stages them, but it does not bring over the commit history.
git checkout B -- .
```

If not all files from branch B should be copied but only those under a specific directory, then you may achieve it like so:

```bash
git checkout C
git checkout B -- path/to/directory
```

## Squash a sequence of commits to a single commit

Say history is `A-B-C-D-E-F` and you want to squash `C-D-E` into one, with `F` still sitting on top:

```
git rebase -i C~1     # or: git rebase -i B, or the SHA before C
```

That opens the todo list oldest-first:

```
pick C
squash D      # or: fixup D
squash E      # or: fixup E
pick F
```

`squash`/`fixup` folds a commit into the one *above* it, so you `pick` the earliest of your run and `squash` the rest into it. `F` stays `pick` and just gets replayed on the new squashed base. Use `fixup` instead of `squash` if you want to discard the folded-in commit messages rather than being dropped into an editor to combine them.

>[!NOTE]
Since you're rewriting `C` onward, every commit after the squash point gets a new SHA — so anything already pushed needs `--force-with-lease`, and if other branches point into that range you'll want `--update-refs` on the rebase to carry them along.

>[!CAUTION]
One caveat on the `C~1` form: if `C` is a merge commit or the very first commit in the repo, `~1` won't resolve cleanly — use the explicit parent SHA or `--root` in the root-commit case.

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

Now follow [Squash one branch to a single commit; then place it in a branch](#squash-one-branch-to-a-single-commit-then-place-it-in-a-branch), where the new branch B is `temp-slice`

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

`my-branch-squashed` now sits on top of `master` as a single commit, so no rebase is needed. Use it in place of `my-branch`.

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
git merge --ff-only my-branch
```

---

# `stash`

## Stash a subset of files by path

```bash
git stash push path/to/file1 path/to/file2
```

---

# `worktree`

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

---

# Inspecting state

## List files modified or yet untracked

```bash
git status --porcelain | cut -c4-
```

> [!NOTE]
> The path starts at column 4, so `cut -c4-` reads it in full. A path containing spaces or special characters is shown double-quoted, e.g. `"my file.txt"`. A rename appears as `old -> new` on a single line.

If you add the following to `~/.gitconfig` then issuing `git changed-files` will constitute an abbreviation

```ini
[alias]
    changed-files = "!git status --porcelain | cut -c4-"
```

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

# Stacked branches/PRs workflow

## After `feature-{k}` branch was approved: what to do next with `feature-{k+1}`

On review: the PRs can be reviewed in parallel, since a reviewer can read each incremental diff independently. But they must *merge* bottom-up — you can't merge #2 before #1, because #2's base branch doesn't exist in `main` yet.

For the merge cascade, here's what happens after PR #1 goes in. GitHub automatically retargets PR #2 to `main` once `feature-a` is merged and its branch deleted. Whether that retarget produces a clean diff depends entirely on the merge strategy:

- With **squash** or **rebase merge**, `main` gets *new* commits with new SHAs. `feature-b` still carries the original individual commits from `feature-a`, so git now sees the same changes represented two different ways. The result is a polluted diff and often phantom conflicts. To fix this you rebase `feature-b` onto the updated `main` with `--update-refs` but excluding `feature-a` from what is included in the rebase.

  ```bash
  git fetch origin main:main
  git rebase --onto main feature-a feature-b --update-refs
  ```

  <details>

  ---

  Say the stack was:

  ```
  main:    A
  feat-1:  A─B─C          (PR1)
  feat-2:  A─B─C─D─E      (PR2)
  ```

  When PR1 is **squash-merged**, GitHub doesn't put B and C into main. It creates one brand-new commit S whose *content* equals B+C combined:

  ```
  main:    A─S            (S = squashed B+C, a fresh SHA)
  ```

  Now `git rebase main` (even with `--update-refs`) tries to replay feat-2's commits B, C, D, E onto S. Rebase *does* have a mechanism to drop commits already present upstream — but it decides that by comparing **patch-ids**. The patch-id of B alone, and of C alone, do not match the patch-id of S (which is B and C *fused into one*). So rebase can't recognize that B and C are already in main. It replays them on top of S, and now feat-2 contains PR1's changes twice — once inside S, once as the replayed B′, C′. That's your bigger diff (and often phantom conflicts).

  This is the key point: **squash-merge specifically defeats rebase's auto-drop**, because squashing changes the patch-id. A rebase-merge or a merge commit would have preserved B and C's patch-ids, and plain `git rebase --update-refs main` would have dropped them cleanly. Squash is the villain here, not your workflow.

  ### Why merging main fixes it

  Merge doesn't try to cancel individual patches — it just advances the merge-base. When you merged main into feat-2, S became an ancestor of feat-2's tip, so GitHub now computes the diff against S instead of A. Everything S already contains (B+C) drops out of the comparison automatically, leaving just D, E. It sidesteps the patch-id problem entirely, which is why it worked where the rebase didn't.

  ### The fix that handles the whole stack at once

  You do **not** need to merge main into every branch. The trick is to stop relying on patch-id auto-drop and instead tell rebase explicitly *which commits to drop* by range, using `--onto`:

  ```
  git fetch origin
  git rebase --onto main feat-1 <tip-branch> --update-refs
  ```

  Read `--onto main feat-1 <tip>` as: "take every commit in `<tip>` that isn't in `feat-1` (so D, E, F, G…) and replant it on `main`." Because B and C are *at or below* feat-1's tip, they're excluded by definition — no patch-id guessing involved. And `--update-refs` moves feat-2, feat-3, … to their new positions in the same pass. One command, whole stack restacked, no per-branch merging.

  So if your stack is feat-2 → feat-3 → feat-4, you check out feat-4 (the tip) and run that one rebase; all three refs move. Then force-push them together:

  ```
  git push --force-with-lease origin feat-2 feat-3 feat-4
  ```

  >[!CAUTION]
  > Two caveats:

  > - `feat-1` in that command must still point at PR1's old tip (commit C). If you've already deleted feat-1, grab C's SHA from `git reflog` or from the (now-merged) PR1 page on GitHub and use the SHA in its place.
  > - Fetch first, so your local `main` actually contains S. Rebasing onto a stale main is a common reason the whole thing silently does nothing.

  ### How to avoid this next time

  The repetition you're fighting is downstream of the squash. If you **rebase-merge or merge-commit the bottom of a stack** instead of squashing it, the lower commits keep their patch-ids, and plain `git rebase --update-refs main` will auto-drop them and restack everything with no `--onto` gymnastics. Reserve squash for the *top* of a stack or for standalone PRs. That's the setting where `--update-refs` delivers the frictionless experience you were expecting.

  ---

  </details>

- With a **merge commit**, `feature-a`'s commits land on `main` with their original SHAs. Since `feature-b` already contains those exact commits in its history, git recognizes them as already-present and the diff stays clean. No rebase strictly needed.

Since squash-merge is the common house style on many teams, the practical loop usually becomes:
1. merge PR #1
2. rebase the rest of the stack onto the new `main`
3. force-push
4. merge PR #2
5. repeat.

This is exactly where `git rebase --update-refs` earns its keep — it rebases the whole stack and moves every intermediate branch ref in one operation, so you don't have to rebase and repoint each branch by hand. You'd run it from the tip of the stack against the freshly-updated `main`, then force-push the branches (`--force-with-lease`).

> [!NOTE]
> Enabling `rebase.updateRefs = true` in your git config makes `--update-refs` the default for every interactive rebase, which is handy when you're living in stacks day to day.

## Editing

Stacked PRs are for breaking one large change into a sequence of smaller, individually reviewable pieces where each piece depends on the one before it. The defining characteristic is that the branches are based on one another in a cascade, not each targeting main independently.

So the structure looks like:

```
main
 └─ feature-a          PR #1: feature-a → main
     └─ feature-b      PR #2: feature-b → feature-a
         └─ feature-c  PR #3: feature-c → feature-b
```

The reason each PR targets its *parent* branch rather than `main` is the diff. If PR #2 targeted `main` directly, GitHub would show it containing all of PR #1's changes too, so the reviewer couldn't tell what's actually new in #2. By targeting the parent, each PR's diff shows only its own incremental changes, which is the whole point of stacking.

> Let's say we have some stacked branches. Let's say that we need to make a change to the first one, how do we propagate it to every other branch?

Propagating a change down the stack just means rebasing the upper branches onto the new tip of the lower one. The thing that makes this painless is `--update-refs`, which moves all the intermediate branch pointers for you in a single rebase instead of you rebasing each branch by hand.

Say your stack is:

```
main → A (feature-a) → B (feature-b) → C (feature-c)
```

### If it's a new commit rather than an amend

Add it and rebase

```bash
git checkout feature-a

# make your change
git add -A && git commit -m "handle empty-input case"

git checkout feature-c

git rebase --update-refs feature-a
```

Now `feature-b` and `feature-c` are replayed on top of `feature-a`'s new tip, and `--update-refs` moves the intermediate pointers.

### If you're editing a commit that already exists in `feature-a`

Do an interactive rebase from the base while sitting on the top branch:

```bash
git checkout feature-c  # top of the stack

git rebase -i --update-refs main
```

The todo list will show every commit from `main` up to `feature-c`, with `update-ref refs/heads/feature-a` and `update-ref refs/heads/feature-b` lines interspersed at the right spots. Mark the commit you want to change as `edit`, make your change, `git commit --amend`, then `git rebase --continue`. When it finishes, all three branch refs point where they should — the change is baked into `feature-a` and `feature-b`/`feature-c` are replayed on top of it.

Finally, since you've rewritten history on every branch, push the whole stack with lease protection:

```bash
git push --force-with-lease origin feature-a feature-b feature-c
```

### If you squash commits / reword messages

```bash
git rebase --onto feature/movex-xyz-src <OLD-src-tip> feature/movex-xyz-test
```

Three arguments to `git rebase --onto`:

- new base = `feature/movex-xyz-src` — the rewritten tip you want to sit on top of.
- old base = `<OLD-src-tip>` — the commit that was -src's tip before you squashed/reworded. Everything up to and including this is discarded from the replay.
- branch = `feature/movex-xyz-test` — what gets replayed.

#### Why plain `git rebase feature/movex-xyz-src feature/movex-xyz-test` fails

That form replays every commit on `-test` that isn't reachable from new `-src`. But the OLD `-src` commits (pre-rewrite) are no longer reachable from new `-src` either, so git tries to replay them too. You get the
old base commits back as duplicates, plus conflicts. `--onto` is what tells git "cut below this old base, keep only what's above it".

#### Finding the `OLD-src-tip`

History rewrite orphaned it, but `reflog` still holds it:

```bash
git reflog feature/movex-xyz-src
```

Look for the entry just before your `squash`/`reword` (commit message will be the pre-squash one). Copy that hash. That is `<OLD-src-tip>`.

#### After

Verify only test commits replayed, no `-src` duplicates:

```bash
git log --oneline feature/movex-xyz-src..feature/movex-xyz-test
git range-diff @{u}...HEAD   # optional, compare old vs new test branch
```
