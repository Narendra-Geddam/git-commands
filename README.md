                    # GIT FROM ZERO HERO


Conventions used:
- Replace UPPERCASE with your own values (e.g., BRANCH, FILE, COMMIT, REMOTE).
- “Scenario” sections show realistic interview-style examples.
- ASCII diagrams only (no colors).
- Works with modern Git (2.x+). Some older behaviors noted where useful.


## 0) CORE CONCEPTS QUICK REFERENCE

HEAD        : Pointer to your current checkout (usually the tip of the current branch).
origin      : Default name of the remote repository URL (e.g., GitHub/GitLab).
Upstream    : The remote branch your local branch tracks (e.g., origin/main).
Commit-ish  : Any reference pointing at a commit (branch, tag, HEAD, hash).
Index/Staging: Area where you “stage” changes before committing.
Working tree: Your actual files on disk.

Fast-forward merge: Target branch has no new commits; pointer just moves forward — no merge commit.
Detached HEAD: You checkout a commit/tag directly (not a branch). New commits won’t belong to a named branch unless you create one.


## 1) HELP & DISCOVERY

```
git help COMMAND
```
```
git COMMAND -h
```
man git-COMMAND
Use-case: Explore flags and examples quickly.

Scenario: “I forgot the flags for git revert.” → `git help revert` prints manual.


## 2) IDENTITY & CONFIGURATION

```
git config [--system|--global|--local] KEY VALUE
```
```
git config --list [--show-origin]
```
Common keys:
  user.name "Your Name"
  user.email "you@example.com"
  core.editor "vim"            # default editor
  init.defaultBranch main      # set default branch name
  pull.rebase true             # pull uses rebase
  fetch.prune true             # auto prune tracking branches
  merge.ff only                # forbid non-fast-forward merges
  color.ui auto                # colored output on supported terminals
  alias.co checkout            # alias

Scenario: Company standardizes on “main”:
  `git config --global init.defaultBranch main`


## 3) STARTING A REPO

```
git init [--initial-branch=main]
```
Use-case: Start a new local repository.
Scenario: New project folder → `git init --initial-branch=main`

```
git clone [--depth N] [--filter=blob:none] [--sparse] URL [DIR]
```
Use-cases:
  - Full clone: `git clone URL`
  - Shallow: `git clone --depth 1 URL` (faster CI)
  - Partial blobs: `git clone --filter=blob:none URL`
  - Sparse checkout: `git clone --sparse URL` then `git sparse-checkout set PATH`

Scenario: CI needs only /app folder:
  `git clone --filter=blob:none --sparse URL repo && cd repo && git sparse-checkout set app`


## 4) IGNORING & ATTRIBUTES

.gitignore patterns:
  node_modules/
  *.log
  !keep.log          # negate ignore
Use-case: Exclude build artifacts.

.gitattributes (behavior per path):
  *.sh text eol=lf
  *.png binary
  docs/** linguist-documentation

Scenario: Windows + Linux team enforces LF:
  `.gitattributes` → `* text=auto eol=lf`


## 5) ADD, STATUS, DIFF

```
git status [-s]
```
```
git add [--all|-A] FILES
```
```
git add -p                  # interactive patch staging
```
```
git diff [--staged] [PATH]  # unstaged vs staged diffs
```

Scenario: Stage only parts of a file:
  `git add -p` → pick hunks to stage.


## 6) COMMIT

```
git commit [-m "msg"] [-a] [--amend] [--no-edit] [--signoff]
```
- -a : stage tracked changes automatically
- --amend : modify the last commit
- --no-edit : keep last message when amending
- --signoff : add “Signed-off-by” (DCO)

Scenarios:
A) Forgot a file → `git add missing.js && git commit --amend --no-edit`
B) Quick commit all tracked changes → `git commit -am "Hotfix"`


## 7) EXPLORING HISTORY: LOG, SHOW, BLAME, SHORTLOG, DESCRIBE, GREP

```
git log [--oneline] [--graph] [--decorate] [--stat] [--since="2 weeks"]
```
```
git log --author="NAME|EMAIL" --grep="TEXT" -- path/
```
```
git show COMMIT [--name-only]
```
```
git blame FILE [-L START,END]
```
```
git shortlog -s -n [--all] [--since=...]
```
```
git describe [--tags] [--abbrev=7]
```
```
git grep "pattern" [--heading] [--break]
```

Scenarios:
A) Who last touched line 120? → `git blame -L 120,120 file.py`
B) Count commits per author → `git shortlog -s -n`
C) Find commits mentioning “invoice” → `git log --grep invoice`


## 8) DIFF ADVANCED

```
git diff BRANCH1..BRANCH2 [PATH]
```
```
git diff --name-only HEAD~3
```
```
git difftool                # use external tool if configured
```

Scenario: PR review locally:
  `git diff main..feature --stat`


## 9) BRANCHING

```
git branch                  # list local branches
```
```
git branch -r               # list remote-tracking
```
```
git branch -a               # all
```
```
git branch NEW              # create
```
```
git branch -m OLD NEW       # rename
```
```
git branch -d BRANCH        # delete (safe)
```
```
git branch -D BRANCH        # delete (force)
```
```
git branch --set-upstream-to=origin/BR target
```

```
git switch BRANCH           # modern switch
```
```
git switch -c NEW           # create + switch
```

```
git checkout BRANCH         # legacy switch
```
```
git checkout -b NEW         # create + switch
```
```
git checkout COMMIT         # detached HEAD
```

Scenario: Start feature:
  `git switch -c feature/payment`


## 10) MERGE

```
git merge BRANCH [--no-ff] [--ff-only] [-m "msg"] [-X ours|theirs]
```
- --no-ff   : create a merge commit even if fast-forward is possible
- --ff-only : fail if merge would require a merge commit
- -X ours   : during conflict, prefer current branch hunks
- -X theirs : during conflict, prefer incoming branch hunks

Scenario: Preserve branch history in PR:
  `git merge --no-ff feature`


## 11) REBASE

```
git rebase BASE            # replay current branch on top of BASE
```
```
git rebase -i HEAD~N       # interactive (squash, edit, reword, drop)
```
```
git rebase --onto NEWBASE OLD_BASE [BRANCH]
```
```
git rebase --abort | --continue | --skip
```
```
git pull --rebase          # rebase instead of merge on pull
```

Scenarios:
A) Linearize before merge:
  `git rebase -i main`
B) Move topic branch from old base to new:
  `git rebase --onto main old-feature-base feature`


## 12) SQUASH

Interactive rebase:
  `git rebase -i HEAD~5` → change lines to `squash` (or `s`)

Merge squashed:
  `git merge --squash feature && git commit -m "Feature squashed"`

Scenario: Clean up noisy WIP commits before PR.


## 13) CHERRY-PICK

```
git cherry-pick COMMIT [COMMIT..COMMIT2] [-n] [-x] [-e] [-m PARENT]
```
- -n/--no-commit : apply changes without creating a commit
- -x             : append "cherry picked from" to message
- -e             : edit message
- -m PARENT      : pick a merge commit using parent number

Scenario: Apply a single bugfix from main to release:
  `git cherry-pick a1b2c3 -x`


## 14) STASH

```
git stash push [-m MSG] [-k|--keep-index] [-p] [-u|--include-untracked] [--all]
```
```
git stash list
```
```
git stash show [-p] [stash@{N}]
```
```
git stash apply [stash@{N}] [--index]
```
```
git stash pop [stash@{N}]    # apply + drop
```
```
git stash drop [stash@{N}]
```
```
git stash clear
```
```
git stash branch NEW [stash@{N}]
```

Scenarios:
A) Switch branch quickly but keep WIP:
  `git stash push -m "WIP checkout" -u`
B) Bring back with index:
  `git stash apply --index`

Conflicts can happen when applying — resolve like a merge.


## 15) UNDO: RESET / REVERT / RESTORE / CHECKOUT

```
git reset [--soft|--mixed|--hard] COMMIT
```
- --soft  : move HEAD; keep staged changes
- --mixed : move HEAD; keep working changes (default)
- --hard  : move HEAD; discard both staged and working changes
Also: `git reset PATH` → unstage paths

Scenario: Undo last commit but keep changes staged:
  `git reset --soft HEAD~1`

```
git revert COMMIT [-m PARENT] [-n]
```
- Safely creates a new commit that inverts the given commit.
- Use -m when reverting a merge commit.

Scenario: Production broke on commit X:
  `git revert X` (no history rewrite).

```
git restore [--staged] PATH [--source=COMMIT]
```
- Discard working changes: `git restore file`
- Unstage: `git restore --staged file`
- Restore from commit: `git restore --source=HEAD~2 file`

Legacy file discard:
  `git checkout -- file`  (older Git)


## 16) FETCH / PULL / PUSH

```
git fetch [REMOTE] [--all] [--prune] [--tags] [--depth N]
```
- --prune : remove deleted remote branches locally
- --depth : shallow fetch for speed

```
git pull [--rebase|--no-rebase] [--ff-only]
```
- `git config pull.rebase true` for linear pulls

```
git push [REMOTE] [BRANCH]
```
```
git push -u REMOTE BRANCH           # set upstream
```
```
git push --force-with-lease         # safe force push
```
```
git push --delete REMOTE BRANCH     # delete remote branch
```
```
git push --tags                     # push all tags
```
```
git push --atomic                   # all-or-nothing
```

Scenarios:
A) First push new branch:
  `git push -u origin feature/login`
B) Rewrite local history then update remote safely:
  `git push --force-with-lease`


## 17) REMOTE MANAGEMENT

```
git remote -v
```
```
git remote add NAME URL
```
```
git remote set-url NAME NEW_URL
```
```
git remote rename OLD NEW
```
```
git remote remove NAME
```
```
git remote prune NAME
```

Scenario: Move from HTTPS to SSH:
  `git remote set-url origin git@github.com:ORG/REPO.git`


## 18) CLEAN WORKING TREE

```
git clean -n           # dry run
```
```
git clean -f           # remove untracked files
```
```
git clean -fd          # remove untracked files + directories
```
```
git clean -fx          # include ignored files
```
```
git clean -fX          # only ignored files
```

Scenario: CI job needs a pristine workspace:
  `git clean -fdx` (be careful!)


## 19) WORKTREES (PARALLEL CHECKOUTS)

```
git worktree add ../repo-feature FEATURE
```
```
git worktree list
```
```
git worktree remove PATH
```

Scenario: Build two branches simultaneously without re-cloning.


## 20) SUBMODULES

```
git submodule add URL PATH
```
```
git submodule update --init --recursive
```
```
git submodule deinit PATH
```
```
git submodule foreach 'git pull'
```

Scenario: Project embeds a library repo that should stay pinned.


## 21) ARCHIVE / EXPORT

```
git archive -o out.zip HEAD [PATH]
```
```
git archive --format=tar --prefix=proj/ v1.0 | gzip > release.tar.gz
```

Scenario: Produce a clean source bundle without .git for distribution.


## 22) PATCH FLOW (format-patch / am / apply)

```
git format-patch -n BASE..HEAD       # create patch files
```
```
git am PATCH.mbox                    # apply emailed patches
```
```
git apply DIFF.patch                 # apply a diff (no commit)
```

Scenario: Submit patches to a project that prefers email workflows.


## 23) GARBAGE COLLECTION & INTEGRITY

```
git gc [--aggressive]
```
```
git fsck
```

Scenario: Repo bloated with rewrites → run `git gc` after large history surgery.


## 24) CREDENTIALS & SECURITY

```
git config --global credential.helper store|cache|manager
```
- store : plaintext on disk (discouraged on shared systems)
- cache : memory cache for a time
- manager : OS keychain (Windows/Mac)

Scenario: Use SSH keys (recommended) instead of HTTPS tokens when possible.


## 25) REFS & REFERENCE TRICKS

HEAD^        : first parent of HEAD
HEAD~2       : two commits before HEAD
BRANCH@{yesterday} : reflog time-based selector
stash@{0}    : latest stash

Scenario: Diff last two commits:
  `git diff HEAD~1..HEAD`


## 26) REFLOG

```
git reflog [show] [--date=relative]
```
```
git reflog expire --expire=now --all   # cleanup (careful)
```
```
git reflog delete HEAD@{N}
```

Scenario: Recover a “lost” commit after hard reset:
  `git reflog` → find hash → `git checkout -b rescue HASH`


## 27) BISECT (FIND BUG-INTRODUCER)

```
git bisect start
```
```
git bisect bad HEAD
```
```
git bisect good COMMIT_OR_TAG
```
# test code; mark good/bad repeatedly
```
git bisect reset
```
# Automation:
```
git bisect run ./test_script.sh
```

Scenario: Intermittent bug introduced in last month → bisect with a test script.


## 28) TAGS (LIGHTWEIGHT, ANNOTATED, SIGNED)

```
git tag v1.0                    # lightweight
```
```
git tag -a v1.0 -m "Release"    # annotated
```
```
git tag -s v1.0 -m "Signed"     # GPG-signed
```
```
git show v1.0
```
```
git push origin v1.0
```
```
git push origin --tags
```
```
git tag -d v1.0
```
```
git push origin --delete v1.0
```

Scenario: CI deploys when a v* tag is pushed.


## 29) RELEASES (HOSTED)

- GitHub/GitLab use tags to create Releases with notes and assets.
Scenario: Build artifacts uploaded to release v2.3 for distribution.


## 30) MERGE CONFLICTS — CAUSES & RESOLUTION

Common causes (besides same-line edits):
- Rename vs modify, delete vs modify, binary files, cherry-pick/stash/revert overlaps.

Resolution steps:
### 1) `git status` to see conflicted files
### 2) Edit files, pick correct hunks
### 3) `git add` each resolved file
### 4) Continue:
   - Merge: `git commit` (if not auto-created)
   - Rebase: `git rebase --continue`
   - Cherry-pick: `git cherry-pick --continue`

Strategy options:
- `git merge -X ours` or `-X theirs` to bias conflict resolution.
- For rebase conflicts repeatedly: `git rebase --skip` if safe to drop.

Scenario: Stash pop conflict after refactor → resolve, `git add .`, `git stash drop`.


## 31) DETACHED HEAD — WORKFLOW

- You checked out a commit/tag directly.
- Commits made here are not on a named branch.

Scenario fix:
`git switch -c hotfix-from-tag` to preserve your commits on a new branch.


## 32) LINE ENDINGS & WHITESPACE

```
git config core.autocrlf input|true|false
```
```
git config apply.whitespace fix|warn|nowarn
```

Scenario: Cross-platform devs set `.gitattributes` and `core.autocrlf` to avoid noisy diffs.


## 33) HOOKS (LOCAL AUTOMATION)

Location: .git/hooks/
Common hooks: pre-commit, commit-msg, pre-push, pre-rebase
- Make executable and script your checks (lint/tests).

Scenario: Block commits without conventional commit messages in `commit-msg` hook.


## 34) SPARSE CHECKOUT (BIG MONOREPOS)

```
git sparse-checkout init [--cone]
```
```
git sparse-checkout set PATHS...
```
Scenario: Only work in `services/api` within a large monorepo.


## 35) LFS (Large File Storage) — mention

```
git lfs install
```
```
git lfs track "*.zip"
```
Use-case: Version large/binary files efficiently.
Scenario: Dataset binaries tracked via LFS to avoid huge repo size.


## 36) COMMON “HOW DO I … ?” SCENARIOS

A) Undo last commit but keep my changes:
   `git reset --soft HEAD~1` (staged) OR `git reset HEAD~1` (unstaged)

B) Modify the last commit message/content:
   `git commit --amend`

C) Restore a deleted local branch:
   Find hash in `git reflog`; `git checkout -b BR HASH`

D) Delete local & remote branch:
   `git branch -d BR` and `git push origin --delete BR`

E) See commits by a user:
   `git log --author="Narendra" --oneline`

F) Apply only one fix from another branch:
   `git cherry-pick HASH`

G) Abort a rebase in the middle:
   `git rebase --abort`

H) Make history linear:
   `git rebase main` then fast-forward merge.

I) Get a clean tree (dangerous):
   `git clean -fdx`

J) Download a single file securely from private repo (GitHub):
   `curl -H "Authorization: Bearer $TOKEN" -L https://api.github.com/repos/OWNER/REPO/contents/PATH -H "Accept: application/vnd.github.v3.raw" -o FILE`

K) Force push safely:
   `git push --force-with-lease`

L) Track upstream for an existing branch:
   `git branch --set-upstream-to=origin/BR BR`

M) Count lines added/removed by author:
   `git log --author="NAME" --shortstat --pretty=tformat:`

N) Generate patch from uncommitted work:
   `git diff > my.patch` (apply later with `git apply my.patch`)

O) Revert a merge commit:
   `git revert -m 1 MERGE_COMMIT` (choose parent 1 or 2)

P) List only files changed between branches:
   `git diff --name-only main..feature`

Q) See what will be pushed:
   `git log --oneline --decorate --graph @{u}..HEAD`

R) Make a branch from a tag to hotfix:
   `git switch -c hotfix v2.0.1`

S) Rename the current branch:
   `git branch -m NEW_NAME`

T) Create signed tag (GPG set up):
   `git tag -s v3.0 -m "Signed release v3.0"`


