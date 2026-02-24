# Exercise 08: Branch Management

## Why Branch Management Matters

If you don't clean up branches, your repo turns into a mess. You end up with
20 stale branches, a tangled graph, and no idea what's current. Good branch
management means creating branches when you need them and deleting them when
you're done.


## Setup

```bash
mkdir ~/git-exercises/ex08
cd ~/git-exercises/ex08
git init
echo "start" > start.txt
git add start.txt
git commit -m "initial commit"
```


## Part 1: Deleting Branches

### The Safe Delete: -d

Create a branch, do some work, and merge it:
```bash
git checkout -b finished-feature
echo "done" > done.txt
git add done.txt
git commit -m "finish the feature"
git checkout main
git merge finished-feature
```

Now the branch has been merged. It's served its purpose. Delete it:
```bash
git branch -d finished-feature
```

**What you'll see:**
```
Deleted branch finished-feature (was xxxxxxx).
```

No complaints. Git knows the work is safe in main.


### The Refused Delete

Create a branch with work that ISN'T merged:
```bash
git checkout -b unfinished-feature
echo "not done yet" > wip.txt
git add wip.txt
git commit -m "work in progress"
git checkout main
```

Try to delete it:
```bash
git branch -d unfinished-feature
```

**What you'll see:**
```
error: the branch 'unfinished-feature' is not fully merged.
```

Git is protecting you. This branch has commits that aren't in main. If you
delete it, that work is gone.


### The Force Delete: -D

If you're SURE you don't need the work:
```bash
git branch -D unfinished-feature
```

**What you'll see:**
```
Deleted branch unfinished-feature (was xxxxxxx).
```

Gone. No getting it back easily.


### The Rule

| Command          | When It Works                              |
|------------------|--------------------------------------------|
| git branch -d    | Only if the branch has been merged (safe)  |
| git branch -D    | Always — force deletes even unmerged work  |

**When you're not sure, use `-d` first.** If Git complains, it's telling you
there's unmerged work. Then you decide: merge it first, or force delete.


## Part 2: Renaming Branches

### Rename Any Branch

```bash
git checkout -b old-name
echo "test" > test.txt
git add test.txt
git commit -m "test commit"
```

Rename it:
```bash
git branch -m old-name better-name
```

Check:
```bash
git branch
```

`old-name` is gone, `better-name` is there.

### Rename the Branch You're On

If you're currently on a branch, you don't need to specify the old name:
```bash
git checkout better-name
git branch -m even-better-name
```

Check:
```bash
git branch
```

**Clean up:**
```bash
git checkout main
git branch -D even-better-name
```


## Part 3: Listing and Inspecting Branches

### See All Branches
```bash
git branch
```

### See Which Branches Are Merged Into Main
```bash
git branch --merged
```

This is useful for cleanup. Any branch listed here (except main) can be
safely deleted with `-d` because its work is already in main.

### See Which Branches Are NOT Merged
```bash
git branch --no-merged
```

These branches have work that hasn't been merged yet. Be careful deleting them.

Let's test this. Create some branches:
```bash
git checkout -b merged-one
echo "m1" > m1.txt
git add m1.txt
git commit -m "merged one"
git checkout main
git merge merged-one

git checkout -b not-merged-one
echo "nm1" > nm1.txt
git add nm1.txt
git commit -m "not merged one"
git checkout main
```

Now check:
```bash
git branch --merged
```
You should see `main` and `merged-one`.

```bash
git branch --no-merged
```
You should see `not-merged-one`.

**Clean up:**
```bash
git branch -d merged-one
git branch -D not-merged-one
```


## Part 4: Deleting Multiple Branches at Once

If you have several branches to clean up, list them in one command:
```bash
git branch -d branch-one branch-two branch-three
```

Or to delete all merged branches except main (useful for big cleanups):
```bash
git branch --merged | grep -v "main" | xargs git branch -d
```

This finds all merged branches, excludes main, and deletes the rest.


## Summary

| Command                        | What It Does                              |
|--------------------------------|-------------------------------------------|
| git branch -d name             | Safe delete (only if merged)              |
| git branch -D name             | Force delete (even if not merged)         |
| git branch -m old new          | Rename a branch                           |
| git branch -m new              | Rename the branch you're on               |
| git branch --merged            | List branches that are merged into current|
| git branch --no-merged         | List branches that are NOT merged         |


## Test Yourself

1. You try `git branch -d my-branch` and Git complains. What does that mean?
2. What's the difference between `-d` and `-D`?
3. How do you rename a branch?
4. How do you see which branches have been merged and can be safely deleted?
5. You want to delete a branch you're sure you don't need, even though it's
   not merged. What command?

### Answers

1. The branch has commits that haven't been merged into your current branch.
   Deleting it would lose that work.
2. `-d` only deletes if merged (safe). `-D` force deletes regardless (risky).
3. `git branch -m old-name new-name`
4. `git branch --merged`
5. `git branch -D branch-name`


## Cleanup

```bash
cd ~
rm -rf ~/git-exercises/ex08
```
