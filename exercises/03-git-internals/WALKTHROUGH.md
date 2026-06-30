# Exercise 03: Git Internals -- What's Inside .git/refs

## Why This Matters

In the first two exercises, I told you "a branch is a pointer to a commit."
In this exercise, you're going to see it with your own eyes. We're going to
look inside the `.git` directory and see exactly how Git tracks branches.

Once you see this, branches will never feel mysterious again.


## Setup

```bash
cd ~/lab/git/git-practice-repo/exercises/03-git-internals/
mkdir work && cd work
git init
git status
```

Create a starting file:
```bash
echo "first file" > first.txt
git add first.txt
git status
git commit -m "first commit"
git status
```


## Step 1: Look Inside .git/refs/heads

```bash
ls .git/refs/heads/
```

**What you'll see:**
```
main
```

That's a file. Not a folder, not a database -- a plain text file. That file IS
the `main` branch.

Now look inside it:
```bash
cat .git/refs/heads/main
```

**What you'll see:** A 40-character hash, something like:
```
a1b2c3d4e5f6a1b2c3d4e5f6a1b2c3d4e5f6a1b2
```

That hash is the commit that `main` points to. That's ALL a branch is -- a file
containing a commit hash.

Verify it matches your latest commit:
```bash
git log --oneline -1
```

The short hash at the start of that line should match the first 7 characters
of what you saw in the file.


## Step 2: Create a Branch and Watch the File Appear

```bash
git checkout -b experiment
git status
```

Now look again:
```bash
ls .git/refs/heads/
```

**What you'll see:**
```
experiment  main
```

A new file appeared. That's your new branch.

```bash
cat .git/refs/heads/experiment
```

**What you'll see:** The SAME hash as main. Because right now both branches
point to the same commit. They haven't diverged yet.


## Step 3: Make a Commit and Watch the Hash Change

```bash
echo "experiment work" > experiment.txt
git add experiment.txt
git status
git commit -m "experiment commit"
git status
```

Now check the branch file:
```bash
cat .git/refs/heads/experiment
```

**What you'll see:** A DIFFERENT hash than before. The `experiment` branch file
updated to point to the new commit.

Check that `main` didn't move:
```bash
cat .git/refs/heads/main
```

**What you'll see:** The same old hash. `main` didn't change because you
committed on `experiment`, not on `main`.

This is the core mechanic:
- When you commit, Git updates the CURRENT branch's file with the new commit hash
- Other branches don't move


## Step 4: Understand HEAD

```bash
cat .git/HEAD
```

**What you'll see:**
```
ref: refs/heads/experiment
```

This is how Git knows which branch you're on. `HEAD` is a file that says
"you are currently on this branch."

Switch branches:
```bash
git checkout main
git status
cat .git/HEAD
```

**What you'll see:**
```
ref: refs/heads/main
```

The file changed. That's ALL that `git checkout` does to track your current
branch -- it rewrites this one tiny file.


## Step 5: Prove Switching Is Just File Changes

Let's prove this is real. Check what HEAD says:
```bash
cat .git/HEAD
```
It should say `ref: refs/heads/main`.

Check what files exist:
```bash
ls
```
You should see `first.txt` only (no `experiment.txt`).

Now switch:
```bash
git checkout experiment
git status
cat .git/HEAD
```

It now says `ref: refs/heads/experiment`.

```bash
ls
```

Now `experiment.txt` is there. Git updated HEAD AND swapped your working files.


## Step 6: See What Happens During a Merge

Switch to main and merge:
```bash
git checkout main
git status
cat .git/refs/heads/main
```
Note the hash.

```bash
git merge experiment
git status
cat .git/refs/heads/main
```

**What you'll see:** The hash CHANGED. It moved forward because the merge
moved `main` to include the new commit.

Check that both point to the same place now:
```bash
cat .git/refs/heads/main
cat .git/refs/heads/experiment
```

Same hash. Both branches point to the same commit after a fast-forward merge.


## Step 7: Delete a Branch and Watch the File Disappear

```bash
ls .git/refs/heads/
```

You should see `experiment` and `main`.

```bash
git branch -d experiment
ls .git/refs/heads/
```

**What you'll see:** Only `main`. The `experiment` file is gone. Deleting a
branch is literally deleting a small text file. That's it.


## Summary -- The Full Picture

| File/Directory        | What It Is                                    |
|-----------------------|-----------------------------------------------|
| .git/refs/heads/      | Folder containing one file per branch         |
| .git/refs/heads/main  | The main branch -- contains a commit hash     |
| .git/HEAD             | Tells Git which branch you're currently on    |

| Action                | What Happens Internally                       |
|-----------------------|-----------------------------------------------|
| Create a branch       | New file appears in .git/refs/heads/          |
| Make a commit         | Current branch's file gets updated with new hash |
| Switch branches       | .git/HEAD gets rewritten to point to new branch |
| Delete a branch       | File gets deleted from .git/refs/heads/       |
| Merge (fast-forward)  | Current branch's file gets updated to new hash |


## Test Yourself

1. Where does Git store branch information?
2. What does the file `.git/refs/heads/main` contain?
3. How does Git know which branch you're currently on?
4. What changes in `.git` when you switch branches?
5. What happens to `.git/refs/heads/` when you delete a branch?

### Answers

1. In `.git/refs/heads/` -- one file per branch
2. A single commit hash -- the commit that `main` points to
3. The `.git/HEAD` file says which branch is current
4. `.git/HEAD` gets rewritten to reference the new branch
5. The file for that branch gets deleted


## Cleanup

```bash
cd ~/lab/git/git-practice-repo/exercises/03-git-internals/
rm -rf work
```
