# Exercise 05: Fetch vs Pull vs Merge

## What Problem Are We Solving?

Everything so far has been local — just you and your machine. But in real life
you work with a team. There's a copy of the repo on a server (GitHub, GitLab,
etc.). That's called the **remote**.

When your coworker pushes changes to the remote, you need to get those changes.
There are three commands involved:

- **git fetch** — Downloads new commits from the remote. Does NOT touch your code.
- **git merge** — Brings those downloaded commits into your branch.
- **git pull** — Does a fetch AND a merge in one step.

The key insight: **pull = fetch + merge.**

That's it. Pull is just a shortcut.


## Why Would You Use Fetch Instead of Pull?

Because fetch lets you **look before you leap.**

With pull, changes get merged into your code immediately. If there's a conflict,
you're dealing with it right now whether you're ready or not.

With fetch, you download the changes but your code stays untouched. You can:
- Look at what changed with `git diff`
- Decide if you're ready to merge
- Merge when YOU choose to

Think of it like mail. Fetch checks your mailbox and brings the letters inside.
Pull checks your mailbox, brings the letters inside, AND opens them all immediately.


## Setup

This exercise uses three separate repos to simulate a team:

1. A "remote" server (like GitHub)
2. Your local repo
3. A "coworker" repo

These sub-repos will all live inside a `work/` subdirectory of the exercise folder.

```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/
mkdir work && cd work
```

### Create the Remote

```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/
mkdir remote-repo
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/remote-repo
git init --bare
```

A bare repo is what servers like GitHub use. It stores Git data but has no
working files.

### Create Your Local Repo

```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/
mkdir my-repo
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/my-repo
git init
git status
git remote add origin ../remote-repo
```

Make a first commit and push:
```bash
echo "project start" > project.txt
git add project.txt
git status
git commit -m "initial commit"
git status
git push origin main
git status
```

### Create the Coworker's Repo

```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/
git clone remote-repo coworker-repo
git -C ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/coworker-repo status
```

Now you have three repos:
- `remote-repo` — the shared server
- `my-repo` — your local copy
- `coworker-repo` — your coworker's local copy


## Step 1: Coworker Makes a Change

```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/coworker-repo
echo "coworker added this" > coworker-file.txt
git add coworker-file.txt
git status
git commit -m "coworker added a file"
git status
git push origin main
git status
```

Now the remote has a commit that YOUR repo doesn't know about yet.


## Step 2: Fetch — Download Without Changing Anything

Go back to your repo:
```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/my-repo
git status
```

First, check your graph:
```bash
git log --oneline --all --graph
```

You should only see your initial commit. Now fetch:
```bash
git fetch origin
git status
```

Check the graph again:
```bash
git log --oneline --all --graph
```

**What you'll see:** A new commit appeared, but look carefully at the labels.
Your `main` is still on the old commit. The new commit is labeled `origin/main`.

```
* xxxxxxx (origin/main) coworker added a file
* xxxxxxx (HEAD -> main) initial commit
```

**What happened:** Fetch downloaded the coworker's commit. You can see it. But
YOUR `main` hasn't moved. Your code is unchanged.

Prove it:
```bash
ls
```

No `coworker-file.txt`. It's not in your working files yet.


## Step 3: Inspect Before Merging

This is the power of fetch. You can see what's coming before you take it:

```bash
git diff main origin/main
```

This shows you exactly what changed between your main and the remote's main.
Press `q` to exit.

```bash
git log main..origin/main --oneline
```

This shows commits that are on `origin/main` but not yet on your `main`.


## Step 4: Merge the Fetched Changes

Now that you've looked and you're ready:
```bash
git merge origin/main
git status
```

Check:
```bash
ls
```

Now `coworker-file.txt` is there. Your main has caught up with the remote.

```bash
git log --oneline --all --graph
```


## Step 5: See That Pull Does Both Steps at Once

Let's have the coworker make another change:
```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/coworker-repo
echo "second change by coworker" > second-file.txt
git add second-file.txt
git status
git commit -m "coworker second commit"
git status
git push origin main
git status
```

Go back to your repo:
```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/work/my-repo
git status
```

This time, use pull:
```bash
git pull origin main
git status
```

**What you'll see:** Git fetches AND merges in one step. `second-file.txt`
appears immediately.

```bash
ls
```

The file is there. No separate fetch and merge needed.

```bash
git log --oneline --all --graph
```


## Step 6: When Fetch Matters — A Real Scenario

This is the scenario where fetch saves you:

1. You're in the middle of writing code (uncommitted changes)
2. You want to know if the remote has new stuff
3. You're NOT ready to merge yet

With fetch:
```bash
git fetch origin
git status
git log main..origin/main --oneline
```

You can see what's new without disrupting your work.

With pull, Git would try to merge immediately — and if there's a conflict with
your uncommitted work, you're in trouble.


## The Comparison

| Command    | Downloads from remote? | Changes your code? | When to use               |
|------------|------------------------|--------------------|-----------------------------|
| git fetch  | Yes                    | No                 | When you want to look first |
| git pull   | Yes                    | Yes (merges)       | When you're ready to merge  |
| git merge  | No                     | Yes                | After a fetch, to apply     |

**pull = fetch + merge**. That's the whole thing.


## Test Yourself

1. What does `git fetch` do?
2. What does `git pull` do?
3. Why might you prefer fetch over pull?
4. After fetching, how do you see what changed on the remote?
5. After fetching, how do you apply those changes to your branch?

### Answers

1. Downloads new commits from the remote. Does NOT change your code.
2. Downloads new commits AND merges them into your current branch (fetch + merge).
3. Fetch lets you inspect changes before applying them. Safer when you're in
   the middle of work or not sure if there will be conflicts.
4. `git diff main origin/main` or `git log main..origin/main --oneline`
5. `git merge origin/main`


## Cleanup

Remove the working directory created for this exercise:

```bash
cd ~/lab/git/git-practice-repo/exercises/05-fetch-pull-merge/
rm -rf work
```
