# Git Branching, Merging & Internals — Practice Repo

## What This Is

This is a hands-on practice repo covering Git branching, merging, and internals.
It is NOT a general Git workshop. It focuses specifically on these topics:

1. **Branches and Merging** — creating branches, fast-forward vs merge commit
2. **Merge Conflicts** — what causes them, how to resolve them manually
3. **Git Internals (.git/refs)** — what branches actually are under the hood
4. **Difftool and Mergetool** — visual tools for comparing and resolving
5. **Fetch vs Pull vs Merge** — working with remote repos and teammates
6. **Rebase** — the other way to combine branches, when to use it vs merge
7. **Git Stash** — temporarily shelving uncommitted work
8. **Branch Management** — deleting, renaming, cleaning up
9. **Git Log Tricks** — useful commands for inspecting history
10. **Full Team Workflow** — a realistic scenario putting it all together

## Prerequisites

You should already be comfortable with:
- `git add`, `git commit`, `git push`, `git pull`
- Basic command line navigation
- Editing files with vim or any text editor

## Ordering

Each exercise creates its own fresh repo and cleans up after itself, so most
can be done independently. However, some build on earlier concepts:

**Do these first (in order):**
- 01 Branches and Merging — foundation for everything
- 02 Merge Conflicts — builds on 01

**These require earlier knowledge:**
- 03 Git Internals — makes more sense after 01
- 06 Rebase — requires understanding merge commits from 01-02
- 10 Full Workflow — uses everything, do this LAST

**These can be done in any order (after 01-02):**
- 04 Difftool and Mergetool
- 05 Fetch vs Pull vs Merge
- 07 Git Stash
- 08 Branch Management
- 09 Git Log Tricks

## How To Use This

Each exercise folder has a single **WALKTHROUGH.md** file. It contains:
- A plain-English explanation of the concept
- Why it matters in real life
- Exact commands to run, with expected output
- Explanations of what each command does and WHY
- "Test Yourself" questions to make sure it stuck
- Cleanup steps so your repo stays tidy

**Do them in order. Type the commands yourself — don't copy-paste.**

## Quick Setup

```bash
mkdir ~/git-exercises
cd ~/git-exercises
```

Then open the first exercise and follow along:
```
exercises/01-branches-and-merging/WALKTHROUGH.md
```

## One Rule

After every exercise, clean up your branches. A messy graph makes everything
harder to understand. Each walkthrough includes cleanup steps — don't skip them.
