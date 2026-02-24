# Exercise 09: Git Log Tricks

## Why Git Log Matters

`git log` is how you understand what happened in a repo. Who changed what,
when, and why. Most people only know `git log` which dumps a wall of text.
Here are the versions that are actually useful.


## Setup

Let's get into our exercise folder and make sure we're starting clean:

```bash
cd ~/lab/git/git-practice-repo/exercises/09-git-log-tricks/
mkdir work && cd work
git init
git status
```

Now let's create a repo with enough history to make the log interesting:

```bash
echo "project start" > app.txt
git add app.txt
git status

git commit -m "initial commit"
git status

echo "added login" > login.txt
git add login.txt
git status

git commit -m "add login page"
git status

echo "added signup" > signup.txt
git add signup.txt
git status

git commit -m "add signup page"
git status

echo "login v2" > login.txt
git add login.txt
git status

git commit -m "update login with validation"
git status

echo "added dashboard" > dashboard.txt
git add dashboard.txt
git status

git commit -m "add dashboard"
git status

git checkout -b feature-nav
git status

echo "navigation" > nav.txt
git add nav.txt
git status

git commit -m "add navigation bar"
git status

echo "nav styles" > nav.css
git add nav.css
git status

git commit -m "style navigation"
git status

git checkout main
git status

echo "hotfix" >> app.txt
git add app.txt
git status

git commit -m "hotfix app crash"
git status

git merge feature-nav -m "merge navigation feature"
git status

git branch -d feature-nav
git status

echo "final cleanup" >> app.txt
git add app.txt
git status

git commit -m "cleanup code"
git status
```

Now you have a repo with multiple commits, a branch, a merge, and different
files touched at different times.


## Trick 1: The Compact View

```bash
git log --oneline
```

**What it does:** Shows each commit on one line — hash and message only.

**When to use:** Quick glance at recent history. This is the log command you'll
use 80% of the time.


## Trick 2: The Visual Graph

```bash
git log --oneline --all --graph
```

**What it does:** Shows all branches as a visual tree with lines connecting
commits to their parents.

**When to use:** Understanding how branches relate. Seeing forks and merges.
This is your best friend during branching and merging work.


## Trick 3: Limit the Number of Commits

```bash
git log --oneline -5
```

**What it does:** Shows only the last 5 commits. Change the number to whatever
you want.

**When to use:** When you only care about recent history and don't want to
scroll through hundreds of commits.

Try different numbers:
```bash
git log --oneline -3
git log --oneline -1
```


## Trick 4: See Which Files Changed

```bash
git log --oneline --stat -5
```

**What it does:** Under each commit, shows which files were touched and how
many lines were added or removed.

**When to use:** "What files did that commit change?" Without this, you'd have
to look at each commit individually.

**Reading the output:**
```
abc1234 add login page
 login.txt | 1 +
 1 file changed, 1 insertion(+)
```

- `login.txt | 1 +` means login.txt had 1 line added
- `+` = additions, `-` = deletions


## Trick 5: See the Actual Code Changes

```bash
git log -p -1
```

**What it does:** Shows the full diff for each commit — the actual lines of
code that were added or removed.

`-p` means "patch" (show the diff). `-1` limits it to the last commit.

**When to use:** "What exactly did that commit change?" Useful for reviewing
specific changes.

Press `q` to exit when you're done reading.

Try seeing the last 3 commits with their diffs:
```bash
git log -p -3
```

**Warning:** Without the number limit, `-p` will show diffs for EVERY commit.
That's a LOT of output. Always limit it.


## Trick 6: Filter by Author

```bash
git log --oneline --author="your-name"
```

Replace "your-name" with whatever name is in your git config.

**What it does:** Only shows commits by that person.

**When to use:** On a team repo, "show me just MY commits" or "show me what
Alice committed this week."

Check what name Git is using for you:
```bash
git config user.name
```

Then use it:
```bash
git log --oneline --author="$(git config user.name)"
```


## Trick 7: Filter by File

```bash
git log --oneline -- login.txt
```

**What it does:** Only shows commits that touched `login.txt`.

**When to use:** "When was this file last changed? Who changed it? What did
they do to it?"

The `--` separates the file path from other options. It's technically optional
in simple cases but good practice.

Try it with different files:
```bash
git log --oneline -- app.txt
git log --oneline -- dashboard.txt
```

Notice `app.txt` shows more commits because it was changed multiple times.


## Trick 8: Combine Filters

You can mix and match:

```bash
git log --oneline --stat -3 -- app.txt
```

"Show me the last 3 commits that touched app.txt, and what files each one changed."

```bash
git log -p -1 -- login.txt
```

"Show me the actual code changes in the last commit that touched login.txt."

```bash
git log --oneline --author="$(git config user.name)" -- login.txt
```

"Show me my commits that touched login.txt."


## Trick 9: See Commits Between Branches

```bash
git log --oneline main..feature-branch
```

**What it does:** Shows commits on `feature-branch` that are NOT on `main`.

**When to use:** "What's on this branch that hasn't been merged yet?"

We don't have an active feature branch right now (we merged it), but remember
this for when you're working with branches. It's very useful with `git fetch`:

```bash
git fetch origin
git log --oneline main..origin/main
```

"What's on the remote that I don't have yet?"


## Quick Reference

| Command                                | What It Shows                        |
|----------------------------------------|--------------------------------------|
| git log --oneline                      | Compact one-line history             |
| git log --oneline --all --graph        | Visual branch tree                   |
| git log --oneline -N                   | Last N commits                       |
| git log --oneline --stat -N            | Which files changed                  |
| git log -p -N                          | Actual code changes                  |
| git log --oneline --author="name"      | Commits by one person                |
| git log --oneline -- filename          | Commits that touched one file        |
| git log --oneline main..branch         | Commits on branch not yet in main    |


## Test Yourself

1. How do you see a compact, one-line-per-commit history?
2. How do you see the visual branch tree?
3. How do you see only the last 3 commits?
4. How do you see which files were changed in recent commits?
5. How do you see the actual lines of code that changed in the last commit?
6. How do you see only commits that touched a specific file?

### Answers

1. `git log --oneline`
2. `git log --oneline --all --graph`
3. `git log --oneline -3`
4. `git log --oneline --stat -3` (or however many you want)
5. `git log -p -1`
6. `git log --oneline -- filename`


## Cleanup

```bash
cd ~/lab/git/git-practice-repo/exercises/09-git-log-tricks/
rm -rf work
```
