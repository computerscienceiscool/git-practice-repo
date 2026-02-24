# Exercise 04: Difftool and Mergetool

## What Are These?

You already know `git diff` — it shows changes in your terminal as `+` and `-`
lines. A **difftool** does the same thing but opens a visual side-by-side view
in a program like vimdiff.

A **mergetool** is similar but specifically for resolving conflicts. Instead of
manually editing the `<<<<<<<` markers, it shows you the different versions in
panes and lets you pick what to keep.

Both are optional. You can always use `git diff` and edit conflicts by hand.
But visual tools can be faster once you're comfortable with them.


## Setup

```bash
mkdir ~/git-exercises/ex04
cd ~/git-exercises/ex04
git init
```

Configure your tools (vimdiff is available on most Linux systems):
```bash
git config diff.tool vimdiff
git config merge.tool vimdiff
git config difftool.prompt false
git config mergetool.prompt false
```

Note: We used `git config` without `--global` so these settings only apply to
this repo. Use `--global` if you want them everywhere.

Create a starting file:
```bash
echo "original content" > demo.txt
git add demo.txt
git commit -m "initial commit"
```


## Part 1: Difftool

### Step 1: Create Something to Diff

Make a change without committing:
```bash
echo "modified content" > demo.txt
```

### Step 2: Use git diff (Terminal Version)

```bash
git diff
```

**What you'll see:**
```
-original content
+modified content
```

Lines starting with `-` were removed. Lines starting with `+` were added.
Press `q` to exit.

This works fine. But for complex changes across many lines, a side-by-side
view is easier to read.

### Step 3: Use git difftool (Visual Version)

```bash
git difftool
```

**What you'll see:** Vimdiff opens with two panes side by side.
- Left pane: The committed version ("original content")
- Right pane: Your working copy ("modified content")

**To exit vimdiff:** Press `Escape`, then type `:qa!` and press Enter.
If that doesn't work, press `Escape` twice, then `:qa!` and Enter.

### Step 4: Undo the Change

```bash
git checkout -- demo.txt
```

### Step 5: Compare Two Branches

Create two branches with different changes:
```bash
git checkout -b branch-x
echo "version from branch-x" > demo.txt
git add demo.txt
git commit -m "branch-x change"

git checkout main
echo "version from main" > demo.txt
git add demo.txt
git commit -m "main change"
```

Compare them:
```bash
git diff main branch-x
```

**What you'll see:** The difference between what's on main vs what's on branch-x.
Press `q` to exit.

Or use the visual version:
```bash
git difftool main branch-x
```

Exit with `Escape` then `:qa!`

**Clean up before Part 2:**
```bash
git branch -D branch-x
```


## Part 2: Mergetool

### Step 6: Create a Conflict

We need a conflict for the mergetool to resolve. The file must exist and be
committed BEFORE either branch edits it.

```bash
echo "base version of the file" > conflict-demo.txt
git add conflict-demo.txt
git commit -m "add conflict demo file"
```

Now create conflicting changes:
```bash
git checkout -b branch-y
echo "edited by branch-y" > conflict-demo.txt
git add conflict-demo.txt
git commit -m "branch-y edit"

git checkout main
echo "edited by main" > conflict-demo.txt
git add conflict-demo.txt
git commit -m "main edit"
```

Try to merge:
```bash
git merge branch-y
```

**What you'll see:**
```
CONFLICT (content): Merge conflict in conflict-demo.txt
Automatic merge failed; fix conflicts and then commit the result.
```

### Step 7: Use Mergetool

Instead of editing the markers by hand, run:
```bash
git mergetool
```

**What you'll see:** Vimdiff opens with multiple panes:

- **LOCAL** (left) — your current branch's version (main)
- **REMOTE** (right) — the incoming branch's version (branch-y)
- **BASE** (top center) — what the file looked like before either branch changed it
- **MERGED** (bottom) — this is where you make your final decision

### Step 8: Resolve in the Mergetool

The bottom pane (MERGED) is the one that matters. Edit it to contain whatever
you want the final result to be.

In vim, you can navigate between panes with `Ctrl+W` then arrow keys.

When you're done editing the bottom pane, save and quit all:
- Press `Escape`
- Type `:wqa` and press Enter

### Step 9: Finish the Merge

After the mergetool closes:
```bash
git commit -m "resolved with mergetool"
```

Check the graph:
```bash
git log --oneline --all --graph
```

**Clean up:**
```bash
git branch -d branch-y
```

**Also clean up .orig files** (mergetool sometimes creates backup files):
```bash
rm -f *.orig
```


## Alternative: Just Edit by Hand

Mergetool is optional. Many developers prefer to just open the file, look at
the `<<<<<<<` markers, edit them manually, and move on. Neither approach is
wrong. Use whatever is faster for you.

The manual approach:
1. Open the file with your editor
2. Find the `<<<<<<<` markers
3. Delete the markers and keep what you want
4. `git add filename`
5. `git commit`


## Test Yourself

1. What's the difference between `git diff` and `git difftool`?
2. How do you compare two branches?
3. When do you use mergetool?
4. In the mergetool, which pane do you actually edit?
5. What command do you run after the mergetool to finish the merge?

### Answers

1. `git diff` shows changes in the terminal. `git difftool` opens a visual
   side-by-side program.
2. `git diff branch-a branch-b` or `git difftool branch-a branch-b`
3. When you have a merge conflict and want a visual tool instead of editing
   the `<<<<<<<` markers by hand.
4. The MERGED pane (bottom). That's the final version that gets saved.
5. `git commit`


## Cleanup

```bash
cd ~
rm -rf ~/git-exercises/ex04
```
