# Exercise 01: Branches and Merging

## What Is a Branch?

A branch is a pointer to a commit. That's it.

It is NOT a copy of your code. When you create a branch, Git does not duplicate
any files. It creates a tiny file that contains a single commit hash. That file
IS the branch. We'll prove this later in Exercise 03.

Think of it like a bookmark in a book. The bookmark doesn't copy the page — it
just marks where you are. When you make a new commit, the bookmark moves forward
to the new page.

Why does this matter? Because it means branches are free. They cost nothing.
You can make 50 branches and Git won't slow down. This is why teams use branches
for everything — every bug fix, every feature, every experiment gets its own branch.


## Why Use Branches?

You've been working on `main` this whole time. That works fine when you're alone.
But imagine this:

- You're halfway through building a login page
- Your boss says "drop everything, there's a critical bug in production"
- You need to fix the bug WITHOUT your half-finished login code getting in the way

Without branches, you're stuck. With branches:

1. Your login work is on a `login` branch
2. You switch to `main`, create a `hotfix` branch
3. Fix the bug, merge it into main, deploy
4. Switch back to `login` and continue where you left off

Nobody's code interferes with anyone else's.


## Setup

Create a fresh working directory with its own Git repo for this exercise:

```bash
cd ~/lab/git/git-practice-repo/exercises/01-branches-and-merging/
mkdir work && cd work
git init
git status
```


## Step 1: See What Branches Exist

Run:
```bash
git branch
```

**What you'll see:** Nothing. A blank line. Git doesn't show a branch until
there's at least one commit.


## Step 2: Create Your First Commit

```bash
echo "hello world" > hello.txt
git status
```

**What you'll see:** `hello.txt` listed as an untracked file. Git sees the new
file but isn't tracking it yet.

```bash
git add hello.txt
git status
```

**What you'll see:** `hello.txt` is now under "Changes to be committed." It's
staged and ready to go.

```bash
git commit -m "first commit"
```

**What you'll see:**
```
[main xxxxxxx] first commit
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
```

Now run:
```bash
git branch
```

**What you'll see:**
```
* main
```

The `*` means "you are currently on this branch." Right now there's only one
branch and you're on it.


## Step 3: Create a Branch and Switch To It

```bash
git checkout -b my-feature
```

**What this does:** Two things in one command:
1. Creates a new branch called `my-feature`
2. Switches you to it

**What you'll see:**
```
Switched to a new branch 'my-feature'
```

Now check where you are:
```bash
git status
```

**What you'll see:** `On branch my-feature` — confirming you switched.

```bash
git branch
```

**What you'll see:**
```
  main
* my-feature
```

The `*` moved to `my-feature`. You're now on the new branch.

**IMPORTANT:** At this moment, both `main` and `my-feature` point to the exact
same commit. Nothing has diverged. They're two bookmarks on the same page.


## Step 4: Make a Commit on the Feature Branch

```bash
echo "feature work" > feature.txt
git status
```

**What you'll see:** `feature.txt` as an untracked file, and you're on branch
`my-feature`.

```bash
git add feature.txt
git status
```

**What you'll see:** `feature.txt` staged and ready to commit.

```bash
git commit -m "add feature work"
```

Now `my-feature` has moved forward by one commit. `main` is still where it was.

Let's see it:
```bash
git log --oneline --all --graph
```

**What you'll see:**
```
* xxxxxxx (HEAD -> my-feature) add feature work
* xxxxxxx (main) first commit
```

This is a straight line. `my-feature` is one commit ahead of `main`. `main`
hasn't moved.


## Step 5: Switch Back to Main

```bash
git checkout main
git status
```

**What you'll see:** `On branch main` — you're back.

Now check what files exist:
```bash
ls
```

**What you'll see:** Only `hello.txt`. No `feature.txt`.

**Why:** `feature.txt` was committed on `my-feature`. When you switched to `main`,
Git updated your working files to match main's latest commit — which doesn't
include `feature.txt`. The file isn't gone. It's safe on the `my-feature` branch.

Switch back to verify:
```bash
git checkout my-feature
ls
```

**What you'll see:** `feature.txt` and `hello.txt`. Both are there.

Switch back to main for the next step:
```bash
git checkout main
git status
```


## Step 6: Your First Merge — Fast-Forward

You're on `main`. You want to bring the feature work into main.

```bash
git merge my-feature
```

**What you'll see:**
```
Updating xxxxxxx..xxxxxxx
Fast-forward
 feature.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 feature.txt
```

**Notice it says "Fast-forward."** This is the first type of merge.

**Why fast-forward?** Because `main` hadn't moved. Only `my-feature` had new
commits. So Git doesn't need to do anything fancy — it just slides the `main`
pointer forward to where `my-feature` is. Like moving a bookmark forward a
few pages.

Check the graph:
```bash
git log --oneline --all --graph
```

**What you'll see:** A straight line. Both branches point to the same commit. No
fork, no merge commit.

**Clean up the branch:**
```bash
git branch -d my-feature
git status
```


## Step 7: Set Up a True Merge (Merge Commit)

For a merge commit to happen, BOTH branches need to have moved forward. Let's
set that up.

Create a new branch:
```bash
git checkout -b new-feature
git status
```

Make a commit on the feature branch:
```bash
echo "new feature code" > new-feature.txt
git add new-feature.txt
git status
git commit -m "add new feature"
```

Now switch to main AND make a commit there too:
```bash
git checkout main
git status
echo "main has moved too" > main-update.txt
git add main-update.txt
git status
git commit -m "update main"
```

Check the graph:
```bash
git log --oneline --all --graph
```

**What you'll see:** A fork. Two branches splitting from a common ancestor.
```
* xxxxxxx (HEAD -> main) update main
| * xxxxxxx (new-feature) add new feature
|/
* xxxxxxx add feature work
* xxxxxxx first commit
```

This is the key difference from before. Both branches have moved.


## Step 8: Merge — This Time It's a Merge Commit

```bash
git merge new-feature
```

Git may open your editor for a commit message. If it does, save and close it
(in vim: `:wq`).

**What you'll see:**
```
Merge made by the 'ort' strategy.
 new-feature.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 new-feature.txt
```

**Notice it does NOT say "Fast-forward" this time.** It says "Merge made by
the 'ort' strategy." This means Git created a merge commit — a special commit
that ties the two branches back together.

**What is the 'ort' strategy?** "ort" stands for "Ostensibly Recursive's Twin."
It's Git's default merge strategy (it replaced the older "recursive" strategy in
Git 2.34+). It looks at three things: the common ancestor commit (where the
branches split), your branch's changes, and the other branch's changes. Then it
combines them. If both branches changed different files (like here), it just
includes both. If both changed the same file in the same place, that's a merge
conflict — which is Exercise 02. You don't need to pick a strategy. Git uses
ort automatically.

Check the graph:
```bash
git log --oneline --all --graph
```

**What you'll see:**
```
*   xxxxxxx (HEAD -> main) Merge branch 'new-feature'
|\
| * xxxxxxx (new-feature) add new feature
* | xxxxxxx update main
|/
* xxxxxxx add feature work
* xxxxxxx first commit
```

Two lines forking and then coming back together. That top commit with the `\`
and `/` is the merge commit.

**Clean up:**
```bash
git branch -d new-feature
git status
```


## The Rule

| What happened                      | Type of merge  |
|------------------------------------|----------------|
| Only feature branch has new commits | Fast-forward   |
| Both branches have new commits      | Merge commit   |

It has nothing to do with which files were changed. It's only about whether
both branches moved forward.


## Test Yourself

Answer these without looking back:

1. What is a branch?
2. When you run `git merge feature`, which branch receives the changes?
3. You create a branch, make 3 commits on it, switch to main, make 0 commits
   on main, then merge. Fast-forward or merge commit?
4. Same as #3, but you make 1 commit on main before merging. Fast-forward or
   merge commit?
5. What does the `*` next to a branch name mean in `git branch` output?

### Answers

1. A pointer to a commit. A tiny file with a hash in it.
2. The branch you're currently on — in this case, you must be on the branch
   you want to merge INTO, not the feature branch.
3. Fast-forward — only the feature branch moved.
4. Merge commit — both branches moved.
5. It marks the branch you're currently on.


## Cleanup

Remove the working directory created for this exercise:

```bash
cd ~/lab/git/git-practice-repo/exercises/01-branches-and-merging/
rm -rf work
```

This resets your repo back to its original state before the exercise.
