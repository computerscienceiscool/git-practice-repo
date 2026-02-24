# Exercise 02: Merge Conflicts

## What Is a Conflict?

A conflict happens when both branches change the **same lines** in the **same file**.
Git doesn't know which version you want, so it stops and asks you to decide.

This is the thing most people are scared of. But it's actually simple once you've
done it a few times. Git tells you exactly where the problem is, and you just
pick what you want to keep.

**Important distinction:**
- Two branches editing DIFFERENT files = no conflict, Git handles it automatically
- Two branches editing the SAME file but DIFFERENT lines = no conflict, Git handles it
- Two branches editing the SAME LINES in the SAME file = CONFLICT, you must resolve it


## Setup

```bash
cd ~/lab/git/git-practice-repo/exercises/02-merge-conflicts/
git status
```


## Step 1: Create a File That Both Branches Will Edit

```bash
echo "line one - original" > shared.txt
git add shared.txt
git status
git commit -m "add shared file"
git status
```

This file now exists on `main`. It's the common starting point.


## Step 2: Create a Branch and Edit the File There

```bash
git checkout -b branch-a
git status
echo "line one - edited by branch-a" > shared.txt
git add shared.txt
git status
git commit -m "branch-a edits shared.txt"
git status
```

You've changed the same line on `branch-a`.


## Step 3: Go Back to Main and Edit the Same File Differently

```bash
git checkout main
git status
echo "line one - edited by main" > shared.txt
git add shared.txt
git status
git commit -m "main edits shared.txt"
git status
```

Now both branches have changed the same line in the same file. The stage is set
for a conflict.

Check the graph:
```bash
git log --oneline --all --graph
```

**What you'll see:** A fork. Both branches diverged from the original commit.


## Step 4: Try to Merge

```bash
git merge branch-a
```

**What you'll see:**
```
Auto-merging shared.txt
CONFLICT (content): Merge conflict in shared.txt
Automatic merge failed; fix conflicts and then commit the result.
```

Don't panic. Git is telling you:
- It tried to merge `shared.txt` automatically
- It couldn't because both branches changed the same part
- It needs you to fix it

Check status:
```bash
git status
```

**What you'll see:**
```
On branch main
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   shared.txt
```

"both modified" = both branches changed this file. This is the conflict.


## Step 5: Look at the Conflict

```bash
cat shared.txt
```

**What you'll see:**
```
<<<<<<< HEAD
line one - edited by main
=======
line one - edited by branch-a
>>>>>>> branch-a
```

Let's break this down piece by piece:

```
<<<<<<< HEAD
```
This marks the START of the conflict. Everything between this line and `=======`
is YOUR current branch's version (main, because that's where HEAD is).

```
line one - edited by main
```
This is what the line looks like on YOUR branch (main).

```
=======
```
This is the divider between the two versions.

```
line one - edited by branch-a
```
This is what the line looks like on the INCOMING branch (branch-a).

```
>>>>>>> branch-a
```
This marks the END of the conflict.


## Step 6: Resolve the Conflict

To resolve it, you edit the file. Remove ALL the markers (`<<<<<<<`, `=======`,
`>>>>>>>`) and keep whatever content you want.

You have three choices:

**Choice A -- Keep main's version:**
Edit the file so it contains only:
```
line one - edited by main
```

**Choice B -- Keep branch-a's version:**
Edit the file so it contains only:
```
line one - edited by branch-a
```

**Choice C -- Combine them or write something new:**
Edit the file so it contains whatever you want:
```
line one - edited by both branches
```

For this exercise, open the file in your editor:
```bash
vim shared.txt
```

Delete the marker lines (`<<<<<<<`, `=======`, `>>>>>>>`), keep whichever
version you prefer, save and exit (`:wq` in vim).

**IMPORTANT:** The file must have NO marker lines left. If you leave any
`<<<<<<<` or `=======` or `>>>>>>>` in the file, you haven't finished resolving.


## Step 7: Finish the Merge

After editing the file, tell Git you've resolved it:
```bash
git add shared.txt
git status
git commit -m "resolve merge conflict"
git status
```

That's it. Conflict resolved. The merge is complete.

Check the graph:
```bash
git log --oneline --all --graph
```

**What you'll see:** The two branches forking and coming back together, with
your merge commit at the top.


## Step 8: Practice -- Another Conflict

Let's do it again to build muscle memory. Clean up first:
```bash
git branch -d branch-a
```

Create a multi-line file this time:
```bash
printf "line one\nline two\nline three\n" > multi.txt
git add multi.txt
git status
git commit -m "add multi-line file"
git status
```

Create a branch that edits line two:
```bash
git checkout -b branch-b
git status
printf "line one\nline two CHANGED BY BRANCH-B\nline three\n" > multi.txt
git add multi.txt
git status
git commit -m "branch-b changes line two"
git status
```

Go to main and edit line two differently:
```bash
git checkout main
git status
printf "line one\nline two CHANGED BY MAIN\nline three\n" > multi.txt
git add multi.txt
git status
git commit -m "main changes line two"
git status
```

Merge:
```bash
git merge branch-b
git status
```

You'll get a conflict. Resolve it the same way:
1. Open the file
2. Remove the markers
3. Keep what you want
4. `git add multi.txt`
5. `git status`
6. `git commit -m "resolve second conflict"`
7. `git status`

Clean up:
```bash
git branch -d branch-b
```


## Step 9: What If You Want to Abort?

If you're in the middle of a conflict and decide "nope, I don't want to merge
right now," you can undo the whole thing:

```bash
git merge --abort
git status
```

This takes you back to before the merge started. Nothing changed. You can try
again later when you're ready.


## The Key Things to Remember

1. A conflict only happens when BOTH branches change the SAME LINES in the SAME FILE
2. Git marks the conflict with `<<<<<<<`, `=======`, `>>>>>>>`
3. Everything between `<<<<<<< HEAD` and `=======` is YOUR branch
4. Everything between `=======` and `>>>>>>> branch-name` is the INCOMING branch
5. To resolve: edit the file, remove markers, keep what you want
6. Then: `git add` the file, `git commit`
7. If you want to bail out: `git merge --abort`


## Test Yourself

1. Both branches edit different files. Is there a conflict?
2. Both branches edit the same file, but different lines. Is there a conflict?
3. Both branches edit line 5 of the same file. Is there a conflict?
4. You're in the middle of a conflict and you want to undo everything. What
   command do you run?
5. After resolving a conflict in a file, what two commands finish the merge?

### Answers

1. No. Git merges them automatically.
2. No. Git is smart enough to merge different parts of the same file.
3. Yes. Git can't decide which version of line 5 to keep.
4. `git merge --abort`
5. `git add filename` then `git commit`


## Cleanup

```bash
git checkout main
git status
git reset --hard
git clean -fd
git status
```
