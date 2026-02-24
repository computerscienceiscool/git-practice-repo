# Exercise 10: Putting It All Together — A Realistic Team Workflow

## What This Exercise Is

This is a full scenario that uses EVERYTHING from exercises 01-09. You'll
play the role of a developer on a team and go through a realistic day of work.

No new concepts. Just practice combining what you've learned.


## The Scenario

You work on a team. The project is a simple web app. You need to:

1. Set up the project
2. Build a feature on a branch
3. Handle an urgent hotfix while your feature is in progress
4. Bring in a coworker's changes
5. Rebase your feature for clean history
6. Merge your feature into main
7. Clean up


## Setup — Create the Team Environment

```bash
cd ~/lab/git/git-practice-repo/exercises/10-full-team-workflow/
```

### The Remote (shared server)
```bash
mkdir remote
cd remote
git init --bare
cd ..
```

### Your Repo
```bash
mkdir my-repo
cd my-repo
git init
git status

git remote add origin ../remote

echo "<!DOCTYPE html><html><body><h1>App</h1></body></html>" > index.html
echo "body { font-family: sans-serif; }" > styles.css
echo "console.log('app started');" > app.js
git add .
git status

git commit -m "initial project setup"
git status

git push origin main
git status
cd ..
```

### Coworker's Repo
```bash
git clone remote coworker-repo
git status
cd ..
```


## Part 1: Start Your Feature

You've been assigned to build a contact form.

```bash
cd ~/lab/git/git-practice-repo/exercises/10-full-team-workflow/my-repo
```

Create a feature branch:
```bash
git checkout -b contact-form
git status
```

Build the feature (make a few commits like you would in real life):
```bash
echo "<form><input type='text' placeholder='Name'></form>" > contact.html
git add contact.html
git status

git commit -m "add contact form HTML"
git status

echo ".form-input { padding: 10px; }" >> styles.css
git add styles.css
git status

git commit -m "add contact form styles"
git status
```

Check your progress:
```bash
git log --oneline --all --graph
```

You should see your `contact-form` branch ahead of `main`.


## Part 2: Your Coworker Pushes Changes

While you're working, your coworker makes changes to main:

```bash
cd ~/lab/git/git-practice-repo/exercises/10-full-team-workflow/coworker-repo
echo "/* navigation styles */" >> styles.css
git add styles.css
git status

git commit -m "add navigation styles"
git status

echo "<nav>Home | About | Contact</nav>" > nav.html
git add nav.html
git status

git commit -m "add navigation HTML"
git status

git push origin main
git status
```


## Part 3: Urgent Hotfix

Your boss messages you: "The app is crashing in production! Fix it NOW!"

You're in the middle of your contact form feature. You're NOT ready to commit.
Let's say you've started editing but haven't committed yet:

```bash
cd ~/lab/git/git-practice-repo/exercises/10-full-team-workflow/my-repo
echo "// TODO: add form validation" >> app.js
git status
```

Your app.js has uncommitted changes. You need to deal with the hotfix.

**Stash your work:**
```bash
git stash push -m "half-finished form validation"
git status
```

Clean working directory. Now switch to main and fix the bug:
```bash
git checkout main
git status

git checkout -b hotfix-crash
git status

echo "console.log('app started safely');" > app.js
git add app.js
git status

git commit -m "fix app crash on startup"
git status
```

Merge the hotfix into main:
```bash
git checkout main
git status

git merge hotfix-crash
git status

git branch -d hotfix-crash
git status
```

Push the fix:
```bash
git push origin main
git status
```


## Part 4: Get Back to Your Feature

Switch back to your feature branch:
```bash
git checkout contact-form
git status
```

Get your stashed work back:
```bash
git stash pop
git status
```

Your half-finished validation code is back in app.js. Finish it and commit:
```bash
echo "function validateForm() { return true; }" >> app.js
git add app.js
git status

git commit -m "add form validation"
git status
```


## Part 5: Bring In Remote Changes With Fetch

Your coworker pushed changes and you pushed a hotfix. Let's see what's on
the remote that you don't have on your feature branch.

```bash
git fetch origin
git status
```

See what's new:
```bash
git log --oneline contact-form..origin/main
```

This shows commits on the remote's main that aren't in your feature branch.
You should see your coworker's commits and your hotfix.

Look at what files changed:
```bash
git diff contact-form origin/main --stat
```


## Part 6: Rebase Your Feature for Clean History

Your feature branch was created before the coworker's changes and the hotfix.
Let's rebase to put your feature commits on top of the latest main.

First, update your local main:
```bash
git checkout main
git status

git merge origin/main
git status
```

Now rebase your feature:
```bash
git checkout contact-form
git status

git rebase main
git status
```

If there's a conflict (likely in styles.css or app.js since both branches
touched them):

1. Open the conflicted file(s)
2. Remove the `<<<<<<<`, `=======`, `>>>>>>>` markers
3. Keep the code you want (probably both changes combined)
4. `git add` the resolved file(s)
5. `git rebase --continue`
6. `git status`

Check the graph:
```bash
git log --oneline --all --graph
```

Your feature commits should be on top of main in a straight line.


## Part 7: Merge Your Feature Into Main

```bash
git checkout main
git status

git merge contact-form
git status
```

This should be a fast-forward (because you rebased).

Check:
```bash
git log --oneline --all --graph
```

Clean straight line.

Push:
```bash
git push origin main
git status
```


## Part 8: Clean Up

Delete the feature branch:
```bash
git branch -d contact-form
git status
```

Verify only main remains:
```bash
git branch
```

Check that everything is clean:
```bash
git status
git log --oneline -5
```

Your history should show a clean sequence of commits: your feature work sitting
on top of the coworker's changes and the hotfix.


## What You Just Practiced

| Concept               | Where You Used It                              |
|-----------------------|------------------------------------------------|
| Creating branches     | contact-form, hotfix-crash                     |
| Switching branches    | Moving between main, contact-form, hotfix      |
| Committing            | Multiple commits on feature and hotfix         |
| Stash                 | Saving half-finished work during hotfix        |
| Merge (fast-forward)  | Merging hotfix into main, feature into main    |
| Fetch                 | Downloading coworker's changes                 |
| Diff                  | Inspecting what changed on the remote          |
| Rebase                | Putting feature commits on top of latest main  |
| Conflict resolution   | Resolving conflicts during rebase              |
| Branch deletion       | Cleaning up hotfix and feature branches        |
| Git log               | Inspecting history throughout the exercise     |


## Do It Again

The best way to make this stick is repetition. Clean up the sub-repos and
reset the exercise directory, then do this entire exercise again from scratch.
Try to do it with fewer glances at the instructions each time.

```bash
cd ~/lab/git/git-practice-repo/exercises/10-full-team-workflow/
rm -rf remote my-repo coworker-repo
git checkout main && git reset --hard
git clean -fd
git status
```

Then start over from the top of this file.

Third time through, try doing it from memory. If you can get through the whole
workflow without looking, you know this stuff.
