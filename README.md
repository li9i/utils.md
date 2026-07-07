Table of Contents
=================

* [`apt`](#apt)
* [Audio](#audio)
* [Between two computers](#between-two-computers)
* [`docker`](#docker)
* [General](#general)
* [`git`](#git)
* [ROS 2](#ros-2)
* [Search](#search)
* [`vim`](#vim)
* [YouTube](#youtube)

## [`apt`](apt.md)

- [List installed packages in chronological order](apt.md#list-installed-packages-in-chronological-order-ascending)
- [List manually installed packages](apt.md#list-manually-installed-packages)
- [Split `apt-get install` to `download` stage and `install` stage](apt.md#split-apt-get-install-to-download-stage-and-install-stage)

## [Audio](audio.md)

- [Extract audio from video](audio.md#extract-audio-from-video)

## [Between two computers](between-two-computers.md)

- [Bandwidth Measurement](between-two-computers.md#bandwidth-measurement)
- [Bridge Networks](between-two-computers.md#bridge-networks)
- [Discover others in subnet](between-two-computers.md#discover-others-in-subnet)
- [Exchange files (netcat)](between-two-computers.md#file-exchange-simple-for-one-off-type-of-situations)
- [Exchange files (python)](between-two-computers.md#file-exchange-lightweight)
- [Exchange files (copyparty)](between-two-computers.md#file-exchange-heavyweight)

## [Claude](claude.md)

  -  [Access session from another directory](claude.md#access-session-from-another-directory)
  -  [Move session from one directory to another](claude.md#move-session-from-one-directory-to-another)

## [Docker](docker.md)

- [Cleanup unnecessary space](docker.md#cleanup-unnecessary-space)
- [Kill running containers](docker.md#kill-running-containers)
- [Reboot Docker](docker.md#reboot-docker)
- [View names of running containers](docker.md#view-names-of-running-containers)
- [View occupied space](docker.md#view-occupied-space)

## [General](general.md)

- [Generate Passwords](general.md#generate-passwords)
- [Run something once on boot](general.md#run-something-once-on-boot)
- [Suppress lines of output on command line](general.md#suppress-lines-of-output-on-command-line)

## [`git`](git.md)

- [Configuration of keys for different repos](git.md#configuration-of-keys-for-different-repos)
- [Install or run pre-commit hooks](git.md#pre-commit)
- [List files modified or yet untracked](git.md#list-files-modified-or-yet-untracked)
- [Remove changes introduced by commit](git.md#remove-changes-introduced-by-commit)
- [Resolve conflicts after merge](git.md#resolve-conflicts-after-merge)
- [Revert multiple commits in one new commit](git.md#revert-multiple-commits-in-one-commit)
- [View file modifications](git.md#view-file-modifications)
- [View ignored files](git.md#view-ignored-files)
- `add`
  - [Add everything except untracked files](git.md#add-everything-except-untracked-files)
  - [Forgot to add file(s) to latest commit and realised before pushing?](git.md#forgot-to-add-files-to-latest-commit-and-realised-before-pushing)
  - [Unstage everything after `add`](git.md#unstage-everything-after-add)
- `checkout`
  - [Checkout file as means of cherry-picking it from another branch](git.md#checkout-file-as-means-cherry-picking-it-from-another-branch)
  - [Checkout file as means of restoring it to past state using commit hash](git.md#checkout-file-as-means-of-restorit-it-to-past-state-using-commit-hash)
- `cherry-pick`
  - [The two branches are in the same repository](git.md#the-two-branches-are-in-the-same-repository)
  - [The two branches are NOT in the same repository](git.md#the-two-branches-are-NOT-in-the-same-repository)
- `diff`
  - [different files across different branches](git.md#diff-different-files-across-different-branches)
  - [Find files changed between commits filtered by regex](git.md#find-files-changed-between-commits-filtered-by-regex)
  - [Find unstaged files in which a particular string was modified](git.md#find-unstaged-files-in-which-a-particular-string-was-modified)
- `fetch`
  - [Fetch by specifying key location](git.md#fetch-by-specifying-key-location)
- `push`
  - [Push by specifying key location](git.md#push-by-specifying-key-location)
  - [Force-push but prevent accidental overwrite](git.md#force-push-but-prevent-accidental-overwrite)
- `rebase`
  - [Rebase and automatically accept changes from branch](git.md#rebase-and-automatically-accept-changes-from-branch)
- `reset`
  - [Take back last commit but keep changes unstaged for further processing](git.md#take-back-last-commit-but-keep-changes-unstaged-for-further-processing)
- `lfs`
  - [Installation](git.md#installation)
  - [Add stuff to](git.md#add-stuff-to)
  - [Migrate existing files to LFS (e.g. large files already committed)](git.md#migrate-existing-files-to-LFS-eg-large-files-already-committed)
  - [Remove commit-unreferenced stuff from](git.md#remove-commit-unreferenced-stuff-from)
- modify
  - [Modify commit message](git.md#modify-commit-message)
  - [Modify commit content](git.md#modify-commit-content)
- `merge`
  - [Merge branch but don't commit changes yet](git.md#merge-branch-but-dont-commit-changes-yet)
- squash
  - [Squash a sequence of commits to a single commit---then place it in a branch](git.md#squash-a-sequence-of-commits-to-a-single-commit-then-place-it-in-a-branch)
  - [Squash commits during merge](git.md#squash-commits-during-merge)
  - [Squash commits during rebase](git.md#squash-commits-during-rebase)
  - [Squash local commits before pushing](git.md#squash-local-commits-before-pushing)
  - [Squash one branch to a single commit---then place it in a branch](git.md#squash-one-branch-to-a-single-commit-then-place-it-in-a-branch)
- `stash`
  - [Stash subset of staged files](git.md#stash-subset-of-staged-files)
- `worktree`
  - [What's a typical workflow?](git.md#worktrees)

## [ROS 2](ros2.md)

- [Cancel action goal from CLI](ros2.md#cancel-action-goal-from-cli)
- [`colcon`](ros2.md#colcon)
- [Migrate signing key](ros2.md@migrate.signing-key)
- [Publish static transform on the fly](ros2.md#publish-static-transform-on-the-fly)
- [Release Package via Apt](ros2.md#release-package-via-apt)
- [Save map published at custom topic](ros2.md#save-map-published-at-custom-topic)
- `topic echo`
  - [Echo specific field of message](ros2.md#echo-field-of-message)
  - [Throttle frequency](ros2.md#throttle-frequency)

## [Search](search.md)

- [`grep` for string in subset of all files](search.md#grep-for-string-in-semi-known-filenames)
- [`grep` for string and get unique matching words](search.md#grep-for-string-and-get-unique-matching-words)
- [`grep` for string and show preceding/succeeding lines](search.md#grep-for-string-and-show-k-preceding-n-succeeding-lines)
- [`grep` for string in files and open each one in separate window](search.md#grep-for-string-in-files-and-open-each-one-in-separate-window)
- [`grep` for string and exclude lines containing specific words](search.md#grep-for-string-and-exclude-lines-containing-specific-words)

## [Shell](shell.md)

- [Pure `bash` bible](shell.md#pure-bash-bible)

## [Vim](vim.md)

- [Delete lines containing a specific pattern](vim.md#delete-lines-containing-a-specific-pattern)
- [Delete lines NOT containing a specific pattern](vim.md#delete-lines-not-containing-a-specific-pattern)
- [Enable backup, undo, swap](vim.md#vim-enable-backup-undo-swap)
- [Replace text and preserve case](vim.md#replace-text-and-preserve-case)

## [YouTube](youtube.md)

- [Download video](youtube.md#download-video)
- [Download video but extract only audio](youtube.md#download-video-but-extract-only-audio)
