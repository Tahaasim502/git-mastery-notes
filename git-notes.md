# Version Control Systems (VCS)

## What is VCS?

A Version Control System tracks changes to files over time by saving **intentional snapshots** (versions) in a database. You decide when a version is worth saving — it does not auto-snapshot on every Ctrl+S.

> Think of it like Google Docs version history — you have to manually name a version to checkpoint it.

**Core purpose:** Save versions at different points, and retrieve any of them whenever needed — whether to fix an error, compare changes, or go back to something that was working.

**Key behavior:** Nothing gets overwritten. Every version is preserved separately. You are always _adding_ to history, not replacing it.

---

## Ctrl+Z vs VCS

| Feature                 | Ctrl+Z (Undo)               | VCS                                |
| ----------------------- | --------------------------- | ---------------------------------- |
| Handled by              | The application (in memory) | A dedicated system (database)      |
| Persists after closing? | No                          | Yes                                |
| Manual or automatic?    | Automatic per action        | Manual (you decide when to commit) |

---

## Real-World Analogy (Word Document)

Most people do VCS manually without realizing it:

```
assignment.docx -> version 1
assignment_final.docx -> version 2
assignment_final_v2.docx -> version 3
assignment_ACTUALLY_final.docx -> version 4

each of these are version file 
if one gets corrupted we can move back to the last version file
```

VCS formalizes and automates this process properly. Instead of renaming files, the system tracks all changes neatly in a database.

---

## Types of VCS

### 1. Local VCS

- All version history stored **only on your own machine**
- Example: RCS (Revision Control System)
- ✅ Simple, fast, no internet needed
- ❌ Single point of failure — hard drive crash = everything lost
- ❌ No collaboration

```
Your Computer
│
├── Working Files
│   └── project.c
│
└── Version Database (local)
    ├── Version 1 (initial)
    ├── Version 2 (added feature)
    └── Version 3 (fixed bug)
```

---

### 2. CVCS — Centralized VCS

- One **central server** holds all version history
- Developers only **checkout the latest snapshot** — not the full history
- ✅ Collaboration possible
- ❌ If the server dies → all history is gone (single point of failure)
- Developers still have their current working files, but version history is lost

---

### 3. DVCS — Distributed VCS

- Every developer has a **complete copy of the entire repository** including full history on their own machine
- If the central server dies → no problem, anyone's machine can restore everything
- ✅ No single point of failure
- ✅ Work can continue even offline
- Example: **Git**

```
Central Server (GitHub/GitLab)
        ↑↓              ↑↓
 Developer A        Developer B
 (full copy)        (full copy)
```

---

## Evolution Summary

|Type|Storage|Collaboration|Failure Risk|
|---|---|---|---|
|Local VCS|Your machine only|❌ None|High|
|CVCS|Central server|✅ Yes|High (server = single point)|
|DVCS|Everyone has full copy|✅ Yes|Low|

---

## Git vs Traditional VCS

### Traditional VCS — Delta Based

- Stores the **list of changes (deltas)** made to each file version by version
- Checks what changed, marks it, stores the diff

### Git — Snapshot Based

- Takes a **full picture (snapshot)** of all files at each commit
- If a file **hasn't changed** → does not store it again, instead **links to the previous identical file** (saves space)
- Git thinks of data as a stream of snapshots over time

---

## Git Integrity — SHA-1 Hashing

Every commit in Git is run through the **SHA-1 hashing algorithm**, producing a unique 40-character checksum:

```
a3f6c2b9d1e4f7a8b2c3d4e5f6a7b8c9d0e1f2a3
```

**How it protects:**

- If anyone tampers with a file — even changing a single character — the hash completely changes
- Git detects the mismatch immediately

> Analogy: Like a seal on an envelope. If someone changes the letter inside, the seal is broken and you know it was tampered with.

**Two uses:**

1. **Integrity check** — detects any corruption or secret edits to history
2. **Identification** — every commit is referred to by its hash, not a name

**Chain structure:**

Each commit's hash is calculated **including the previous commit's hash**. This means:

- Tampering with an old commit → its hash changes
- That breaks the next commit's hash
- Which breaks the one after → entire chain collapses

> This chaining concept is the same idea behind **blockchain**.

**Every modification, deletion, and addition** gets its own unique hash. Your history looks like:

```
a3f6c2b  →  added introduction
d4e7f1a  →  deleted paragraph 2
b9c2d3e  →  modified conclusion
```

Nothing can change in Git without it knowing.

---
## 3 States in Git

Every file in Git lives in one of three states:

1. **Modified** — changes made to the file but not committed yet
2. **Staged** — changes marked and added to the index (staging area), ready for the next commit
3. **Committed** — data is safely stored in the local database (.git directory)

### Flow

```
Modified → Staged → Committed
```

### With commands

```
modify file → git status → git add → git status → git commit
```

> Always run `git status` between each step — it tells you exactly where you are at all times.

---

## The Three Areas

|Area|What it is|
|---|---|
|Working Directory|Where you make changes to files|
|Staging Area|Where you prepare changes before committing|
|.git Directory (Repository)|Where Git stores all history, metadata and snapshots|

### Visual

```
Working Directory → (git add) → Staging Area → (git commit) → .git Directory
                  ← (checkout) ←────────────────────────────────────────────
```

---

## .git Directory

- Created automatically when you run `git init`
- Hidden folder inside your project
- Stores all metadata and the object database (full version history)
- If you delete it → Git forgets everything, folder becomes a regular directory
- Verify it exists:

```bash
ls -a
```

---

## Key Commands Explained

### `git`

Command that calls the Git program. Every Git command starts with `git`.

### `config`

Subcommand telling Git what to do — in this case access configuration settings.

```bash
git config → opens Git settings
```

### `--global`

A flag telling Git where to save the setting.

|Flag|Scope|
|---|---|
|`--system`|Every user on the computer|
|`--global`|Your user account only (all repos)|
|`--local`|One specific repo only|

> `-` is for single letter flags (e.g. `-m`) `--` is for full word flags (e.g. `--global`)

---

## Git Configuration

Run once, applies to all your repos:

```bash
git config --global user.name ""
git config --global user.email ""
git config --global core.editor "code --wait"
git config --global init.defaultbranch main
```

Verify everything is set:

```bash
git config --list
git config init.defaultbranch    # check specific setting
```

---

## Local Repo vs Cloning

### Local Repo (git init)

1. Create a directory
2. Navigate into it
3. Run `git init` → makes the directory the main repo (creates hidden `.git` file for tracking)
4. Create a file
5. `git commit -m "message"`
6. `git log --oneline` → shows commit history

### Clone a Repo (git clone)

- Downloads an existing repo from GitHub including its **full history**

```bash
git clone <url>
```

|Command|When to use|
|---|---|
|`git init`|Starting a fresh repo from scratch|
|`git clone <url>`|Copying an existing repo from GitHub|

---

## Core Commands

```bash
git init                        # initialize a new repo
git status                      # check the state of your files
git add <filename>              # stage a specific file
git add .                       # stage all changed files
git commit -m "your message"    # commit staged changes
git log                         # full commit history
git log --oneline               # condensed commit history
```

---

## The Golden Workflow

```
modify file → git status → git add → git status → git commit → git log --oneline
```

```
git status                      # see that notes.txt is modified
git add notes.txt               # stage it
git status                      # confirm it is staged
git commit -m "your message"    # commit it
git log --oneline               # confirm commit was saved

```

---

## Pushing Local Repo to GitHub

When you create a repo locally it only exists on your machine. To put it on GitHub:

1. Go to GitHub → click **New** → name the repo → do NOT initialize with README
2. Run:

```bash
git remote add origin https://github.com/Tahaasim502/my_project.git
git push -u origin main
```

> `git remote add origin` — links your local repo to GitHub `git push -u origin main` — pushes your commits up and sets tracking

---

## Notes on Warnings

```
LF will be replaced by CRLF
```

This is fine — it is `core.autocrlf=true` doing its job, converting line endings between Windows and Linux automatically. Ignore it.

---
## Branch

- Commit is a save point of the project.
- Branch, on the other hand, is a pointer to the latest commit.
- When we create a new branch, it does not copy the project. It simply points to the current commit.
- When we commit after switching to that branch, only the branch pointer moves forward. This allows us to work on new features before merging it into main.

```
C0 - C1 - C2  <- main
```

Branch - login

```
C0 - C1 - C2  <- main, login
```

We need to switch to the branch first using `git checkout login` — this moves  HEAD(current commit) to point at `login`. Once that's done, we can start committing, and only `login` moves forward while `main` stays put:

```
C0 - C1 - C2  <- main
              \
               C3  <- login, HEAD
```

commands:
1. git branch branchname
2. git checkout branchname 

---

## Merge 

- A merge commit is a commit with **two parents** (instead of the usual one), which is how it "combines" the histories of both branches.
- `git merge <branch>` needs to be run **while standing on the branch you want to move forward**. So if you're on `main` and run `git merge login`, it's `main` that gets the new merge commit and moves forward — `login` stays exactly where it was.
- This only creates a real merge commit if the two branches have diverged (both have new commits since they split). If `main` hasn't moved at all since `login` branched off, Git just does a **fast-forward** — it moves `main`'s pointer up to `login`'s commit with no actual merge commit created.
```
C0 - C1 - C2  <- main
              \
               C3 - C4 <- login, HEAD


After merge
C0 - C1 - C2 <- main
              \
               C3 - C4 <- login, HEAD
		               \  
				          M <- Merge commit

```

---
## Rebase 
- It is same as merge but a bit different rather than merging parents we rebase it on top of the main.
- command:
	- git rebase main 
---
## Detach Head
- It is used for testing commits 
- we use git checkout branchname or the targets name.
---
## Hashes

- It uses  hash to identify every commit 
- Commit has a unique ID because every hash is different 
- Hence git is smart enough to find the hash using some characters only.
- How do we find the hash?
	- We use git log
	- This will give us all the characters and the name and the changes that took place.
----
## Relative commit 

A way to move around git history.
There are two ways of doing this :-
1. Moving around once with ^:
	1. git checkpoint HEAD^
2. Moving around number of times with ~:
	1. git checkpoint HEAD~2
3. HEAD - is the current working commit 
4. ^ - go back previous commit once 
5. ~ - go back number of commits
6. Force branch:-
	1. git branch -f branchname target
	2. this way rather than moving single by single or counting how many times we need to move we can just specify the target and the pointer will move accordingly.
---
## Revert and Reset

1. **Revert** — creates a new commit that reverses the changes.
2. **Reset** — removes commits from history.

```
A - B - C - D  <- main
```

Suppose there are some bugs in D.

Commands:

1. `git revert D` → `A - B - C - D - R`, where R is the new commit where the reversed changes take place.
2. `git reset HEAD^` → `A - B - C`, D disappears as if it was never there.

- Rather than moving back the pointer in reset or going back in history, what revert does is that it undo the changes in D and creates a new commit where every commit changes are saved.
---
## Git Cherry Pick
Rather than moving the whole branch into main we are selecting specific commits from another branch and dropping it into the main branch.
command:
git cherry-pick commit commit....

---
## Git rebase -i
A command that displays a GUI interface that helps us to pick, drag and dop specific commits, which is easier to visualize and structure our commits.
command:
git rebase -i HEAD~ number.

---
## New commands
1. git commit --amend:
	1. This command is used for committing the most recent commit
---
## Tags and Describe 
Tags: they act as anchor tags
- Used for marking specific commits
- git tag v1 # if your tagging the current commit
- git tag v2 c2 # if your tagging a specific commit

Describe: they are used for defining the tags
for eg: v1.0-3-g8f4c9a1

Broken down:
- `v1.0` — the nearest ancestor tag it found
- `3` — you are **3 commits ahead** of that tag
- `g8f4c9a1` — the short hash of your current commit (the `g` just stands for "git")
command:
git describe main

---
## Remote 
- They act as a reference to another copy of the repo. Can function like a backup, the real purpose of it is collaboration
- git clone is used for cloning repos.
- ### What is `o/`?
	- You may be wondering what the leading `o/` is for on these remote branches. Well, remote branches also have a (required) naming convention -- they are displayed in the format of:
	- `<remote name>/<branch name>`
-  **`git clone <url>`** → used once, to download a repo for the first time
- **`git fetch`** → downloads new commits from the remote, but doesn't touch your local branches
- **`git pull`** → fetch + merge in one step, updates your current branch with remote changes
- **`git push`** → sends your local commits up to the remote
- `git push origin main` -> this works more like local : remote 
	- This command is mostly used for remote repo
	- translates to this in English:
	- Go to the branch named "main" in my repository, grab all the commits, and then go to the branch "main" on the remote named "origin". Place whatever commits are missing on that branch and then tell me when you're done.
	- we can use -u also, the work origin is necessary
	- what if we want the source and destination to be different then we use
		- git push origin source:destination
- git fetch origin main -> this works the opposite remote : local

---

