# Exercise 07: Git Stash

## What Is Stash?

Think of a drawer in your desk. You're working on something — papers spread
out everywhere. Someone walks in and needs your desk right now. You sweep
everything into the drawer, deal with their thing, then pull your papers
back out.

That's stash.

**`git stash`** = sweep your uncommitted changes into the drawer
**`git stash pop`** = pull them back out

Your working directory goes back to clean (matching the last commit), and
your half-finished work is safely stored until you want it back.


## Why Does Stash Exist?

Because sometimes you need to do something urgent, but you're not ready to
commit. Your code is half-written, maybe it doesn't even compile. You don't
want a commit that says "half finished broken stuff" in your history.

Common situations:

1. You're coding a feature. Boss says "pull the latest and check something."
   You can't pull with uncommitted changes in the way. Stash, pull, pop.

2. You're on the wrong branch. You started coding on main but should be on
   a feature branch. Stash, switch branches, pop.

3. You want to quickly test something with clean code. Stash your changes,
   test, then pop them back.


## Setup

```bash
mkdir ~/git-exercises/ex07
cd ~/git-exercises/ex07
git init
echo "original code" > app.txt
git add app.txt
git commit -m "initial commit"
```


## Step 1: Create Uncommitted Work

Imagine you're in the middle of adding a feature:
```bash
echo "half-finished login feature" >> app.txt
```

Check status:
```bash
git status
```

**What you'll see:**
```
modified:   app.txt
```

Your file has changes that aren't committed. You're in the middle of work.


## Step 2: Stash It

Your boss says "check something on main right now." You're not ready to commit.

```bash
git stash
```

**What you'll see:**
```
Saved working directory and index state WIP on main: xxxxxxx initial commit
```

"WIP" = Work In Progress. Git saved your changes.

Now check:
```bash
git status
```

**What you'll see:**
```
nothing to commit, working tree clean
```

Your working directory is clean. It's like you never made those changes.

Look at the file:
```bash
cat app.txt
```

**What you'll see:**
```
original code
```

Your half-finished line is gone. But it's NOT deleted — it's in the stash,
waiting for you.


## Step 3: Do the Urgent Thing

With a clean working directory, you can now do whatever you need:

```bash
# Simulate doing some urgent work
echo "urgent hotfix" > hotfix.txt
git add hotfix.txt
git commit -m "emergency hotfix"
```

Your urgent work is done. Now you want your half-finished feature back.


## Step 4: Pop Your Stash

```bash
git stash pop
```

**What you'll see:**
```
On branch main
Changes not staged for commit:
        modified:   app.txt

Dropped refs/stash@{0} (xxxxxxx)
```

"Dropped" means the stash entry was removed after applying it. Your changes
are back in your working directory.

Check the file:
```bash
cat app.txt
```

**What you'll see:**
```
original code
half-finished login feature
```

Your half-finished work is back, exactly how you left it. You can continue
working on it.


## Step 5: Clean Up Before the Next Part

```bash
git checkout -- app.txt
```

This throws away the uncommitted change (since we're just practicing).


## Step 6: Stash Across Branches

Here's a useful trick. You can stash on one branch and pop on a different one.

Imagine you accidentally started working on main instead of a feature branch:

```bash
echo "this should be on a feature branch" >> app.txt
```

You realize you're on the wrong branch. Stash your work:
```bash
git stash
```

Create the correct branch and switch to it:
```bash
git checkout -b correct-branch
```

Pop the stash here:
```bash
git stash pop
```

Now your changes are on the correct branch:
```bash
git status
cat app.txt
```

Your changes are here on `correct-branch`, not on main. Commit them:
```bash
git add app.txt
git commit -m "feature work on correct branch"
```

**Clean up:**
```bash
git checkout main
git branch -d correct-branch
```


## Step 7: Stash With a Message

If you stash multiple times, it helps to label them:

```bash
echo "login changes" >> app.txt
git stash push -m "working on login"
```

See your stash list:
```bash
git stash list
```

**What you'll see:**
```
stash@{0}: On main: working on login
```

The message helps you remember what's in the stash.

Pop it:
```bash
git stash pop
git checkout -- app.txt
```


## Step 8: The Full Stash Workflow

Let's run through the most common real-life scenario one more time, end to end.

You're working on a feature:
```bash
echo "new navigation bar code" >> app.txt
echo "nav styles" > nav.css
git add nav.css
```

Notice: `app.txt` is modified but not staged. `nav.css` is staged but not
committed. Stash saves BOTH:

```bash
git stash
git status
```

Clean. Both changes are stashed.

Do something urgent:
```bash
echo "patch" > patch.txt
git add patch.txt
git commit -m "urgent patch"
```

Get your work back:
```bash
git stash pop
git status
```

**What you'll see:** Both `app.txt` (modified) and `nav.css` (staged) are
back. Stash preserved the state of both files.

**Clean up:**
```bash
git checkout -- app.txt
git reset HEAD nav.css
rm nav.css
```


## Summary

| Command                       | What It Does                              |
|-------------------------------|-------------------------------------------|
| git stash                     | Hide all uncommitted changes              |
| git stash pop                 | Bring the changes back and remove from stash |
| git stash list                | See what's in the stash                   |
| git stash push -m "message"  | Stash with a descriptive label            |
| git stash drop                | Throw away the top stash entry            |

The flow:
1. You're in the middle of work
2. Something urgent comes up
3. `git stash` — changes go into the drawer
4. Deal with the urgent thing
5. `git stash pop` — changes come back out


## Test Yourself

1. What does `git stash` do?
2. What does `git stash pop` do?
3. Can you stash on one branch and pop on a different branch?
4. You have uncommitted changes and need to pull from the remote. What do you do?
5. What's the alternative to stash if your work IS ready to save?

### Answers

1. Takes all uncommitted changes and hides them. Working directory goes back
   to the last commit's state.
2. Brings the stashed changes back into your working directory and removes
   them from the stash.
3. Yes. Git doesn't care which branch you pop on.
4. `git stash`, then `git pull`, then `git stash pop`.
5. Just commit it. Stash is for when you're NOT ready to commit.


## Cleanup

```bash
cd ~
rm -rf ~/git-exercises/ex07
```
