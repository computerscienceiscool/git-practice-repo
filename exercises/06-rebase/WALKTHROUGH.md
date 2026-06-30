# Exercise 06: Rebase

## What Is Rebase?

Rebase is the OTHER way to combine branches. You already know merge. Here's
the difference:

**Merge** creates a merge commit that ties two branches together. The history
shows the fork and rejoin:
```
* merge commit
|\
| * feature commit
* | main commit
|/
* starting point
```

**Rebase** takes your commits and replays them ON TOP of the other branch.
No fork. No merge commit. Clean straight line:
```
* feature commit (replayed)
* main commit
* starting point
```

Same result — your code ends up combined. But the history looks different.


## Why Use Rebase?

Clean history. Some teams prefer a straight-line history because it's easier
to read and understand. With merge, if you have 10 feature branches, you get
10 merge commits and a tangled web of forks. With rebase, it's a clean
straight line.

But rebase has a rule you must follow. We'll get to that.


## Setup

```bash
cd ~/lab/git/git-practice-repo/exercises/06-rebase/
mkdir work && cd work
git init
git status
```

```bash
echo "base content" > base.txt
git add base.txt
git status
git commit -m "initial commit"
git status
```


## Step 1: Create Two Diverging Branches

```bash
git checkout -b feature
git status
echo "feature work" > feature.txt
git add feature.txt
git status
git commit -m "feature commit"
git status
```

```bash
git checkout main
git status
echo "main moved forward" > main-new.txt
git add main-new.txt
git status
git commit -m "main moved forward"
git status
```

Check the graph:
```bash
git log --oneline --all --graph
```

**What you'll see:** A fork.
```
* xxxxxxx (HEAD -> main) main moved forward
| * xxxxxxx (feature) feature commit
|/
* xxxxxxx initial commit
```

Both branches have diverged. If you merged right now, you'd get a merge commit.
Instead, we're going to rebase.


## Step 2: Rebase

The rule: **you rebase FROM the feature branch.** You switch to the branch
whose commits you want to move.

Think of it this way: "I want to take MY commits and put them on top of main."
So you need to be on YOUR branch.

```bash
git checkout feature
git status
git rebase main
git status
```

**What you'll see:**
```
Successfully rebased and updated refs/heads/feature.
```

Now check the graph:
```bash
git log --oneline --all --graph
```

**What you'll see:**
```
* xxxxxxx (HEAD -> feature) feature commit
* xxxxxxx (main) main moved forward
* xxxxxxx initial commit
```

The fork is GONE. Your feature commit is now sitting on top of main's latest
commit. Clean straight line.


## Step 3: Notice the Hash Changed

Look at the hash of your feature commit. It's different from before the rebase.

**Why?** Because rebase didn't MOVE your commit. It created a NEW COPY of it
in a different spot. The old commit is gone (well, it's technically still in
Git's garbage collection for a while, but effectively gone).

This is important and it's the source of the big safety rule.


## Step 4: Finish With a Fast-Forward Merge

Now switch to main and merge:
```bash
git checkout main
git status
git merge feature
git status
```

**What you'll see:**
```
Fast-forward
```

**Why fast-forward?** Because after the rebase, `feature` is just ahead of
`main` in a straight line. Main hasn't moved since the rebase. So Git just
slides the pointer forward.

This is the rebase workflow:
1. Both branches diverge
2. Rebase replays your commits on top of the other branch
3. Merge is now a fast-forward — clean, no merge commit

Check the graph:
```bash
git log --oneline --all --graph
```

Completely straight line. Both branches at the same spot.

**Clean up:**
```bash
git branch -d feature
git status
```


## Step 5: Practice Again

Repetition is how this sticks. Do it again:

```bash
git checkout -b feature-two
git status
echo "feature two work" > f2.txt
git add f2.txt
git status
git commit -m "feature two commit"
git status

git checkout main
git status
echo "more main work" > main2.txt
git add main2.txt
git status
git commit -m "more main work"
git status
```

Check the graph — you should see a fork.
```bash
git log --oneline --all --graph
```

Now rebase:
```bash
git checkout feature-two
git status
git rebase main
git status
```

Check the graph — fork should be gone, straight line.
```bash
git log --oneline --all --graph
```

Merge:
```bash
git checkout main
git status
git merge feature-two
git status
```

Should say fast-forward.

**Clean up:**
```bash
git branch -d feature-two
git status
```


## The Big Safety Rule

**Never rebase commits that other people already have.**

When you rebase, Git creates NEW copies of your commits with NEW hashes.
If your coworker already pulled your original commits, and then you rebase
and push, there are now two versions of the same work with different hashes.
Git gets confused. Your coworker gets confused. Things break.

**The simple rule:**

| Situation                                    | Use     |
|----------------------------------------------|---------|
| Your local branch, nobody else has seen it   | Rebase is safe |
| Branch is pushed and others are using it     | Use merge instead |

A common team workflow:
1. Work on a local feature branch
2. Before merging into main, rebase onto main for clean history
3. Merge into main (will be fast-forward)

But if you've pushed the branch and opened a pull request and others are
reviewing it — use merge, not rebase.


## Merge vs Rebase — When to Use Which

| You want...                                  | Use     |
|----------------------------------------------|---------|
| Clean straight-line history                  | Rebase  |
| Preserve the exact history of what happened  | Merge   |
| To combine a local branch nobody has seen    | Rebase  |
| To combine a shared/pushed branch            | Merge   |
| Simple and safe                              | Merge   |
| Tidy and professional                        | Rebase  |


## Test Yourself

1. What does rebase do to your commits?
2. Which branch do you switch to before rebasing?
3. After a rebase, what type of merge happens when you merge into main?
4. Why shouldn't you rebase commits others have already pulled?
5. Your feature branch is local and nobody has seen it. Rebase or merge?
6. Your feature branch is pushed and your coworker is reviewing it. Rebase
   or merge?

### Answers

1. Takes your commits and replays them on top of the other branch, creating
   new copies with new hashes.
2. The feature branch — the one whose commits you want to move.
3. Fast-forward, because the feature branch is now ahead of main in a
   straight line.
4. Because rebase creates new commit hashes. The old hashes your coworker
   has no longer match, causing confusion and broken history.
5. Rebase — nobody will be affected by the rewritten hashes.
6. Merge — the branch is shared, rebasing would break things for others.


## Cleanup

```bash
cd ~/lab/git/git-practice-repo/exercises/06-rebase/
rm -rf work
```
