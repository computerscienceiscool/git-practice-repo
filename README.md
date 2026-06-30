# Git for Teams — Practice Repo

## What This Is

A hands-on practice repo for teams transitioning from solo Git usage to
working together on shared repositories. The workshop covers what you need
to know when your code starts colliding with other people's code.

## Prerequisites

You should already be comfortable with:
- `git add`, `git commit`, `git push`, `git pull`
- Basic command line navigation
- Editing files with vim or any text editor

## The Workshop (30 minutes)

These three exercises are the core workshop. Do them in order:

1. **[Branches and Merging](exercises/01-branches-and-merging/WALKTHROUGH.md)** — Everyone works on their own branch so you don't step on each other
2. **[Merge Conflicts](exercises/02-merge-conflicts/WALKTHROUGH.md)** — When you DO step on each other, here's how to fix it without panicking
3. **[Fetch vs Pull vs Merge](exercises/05-fetch-pull-merge/WALKTHROUGH.md)** — Your coworker pushed code. Now what?

## Continue Learning

These exercises are available for self-study after the workshop. Each one is
self-contained with its own walkthrough, commands, and cleanup steps.

**Recommended after the workshop:**
- 03 [Git Internals (.git/refs)](exercises/03-git-internals/WALKTHROUGH.md) — What branches actually are under the hood
- 10 [Full Team Workflow](exercises/10-full-team-workflow/WALKTHROUGH.md) — A realistic scenario putting it all together (do this last)

**In any order:**
- 04 [Difftool and Mergetool](exercises/04-difftool-and-mergetool/WALKTHROUGH.md) — Visual tools for comparing and resolving
- 06 [Rebase](exercises/06-rebase/WALKTHROUGH.md) — The other way to combine branches
- 07 [Git Stash](exercises/07-git-stash/WALKTHROUGH.md) — Temporarily shelving uncommitted work
- 08 [Branch Management](exercises/08-branch-management/WALKTHROUGH.md) — Deleting, renaming, cleaning up
- 09 [Git Log Tricks](exercises/09-git-log-tricks/WALKTHROUGH.md) — Useful commands for inspecting history

## Quick Setup

Clone the repo and navigate to it:

```bash
git clone https://github.com/computerscienceiscool/git-practice-repo.git
cd git-practice-repo
```

Then open the first exercise and follow along:
```
cd exercises/01-branches-and-merging/
```

## How Each Exercise Works

Each exercise folder has a single **WALKTHROUGH.md** file. It contains:
- A plain-English explanation of the concept
- Why it matters in real life
- Exact commands to run, with expected output
- Explanations of what each command does and WHY
- "Test Yourself" questions to make sure it stuck
- Cleanup steps so your repo stays tidy

Every exercise creates its own `work/` directory so nothing interferes with
the practice repo itself. Type the commands yourself — don't copy-paste.

## One Rule

After every exercise, clean up. Each walkthrough includes cleanup steps at
the end — don't skip them.
