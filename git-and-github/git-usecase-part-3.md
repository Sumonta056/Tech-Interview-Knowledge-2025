# Git usecase (Part-3)

### How to create merge conflict ?

```
# Create a fresh repo
mkdir git-practice && cd git-practice
git init
echo "Hello world" > app.txt
git add . && git commit -m "initial commit"

# Create a feature branch (like you would at work)
git checkout -b feature/add-greeting

# Make a change
echo "Good morning!" >> app.txt
git add . && git commit -m "feat: add greeting message"

# Now imagine main got updated by a teammate
git checkout main
echo "Version 1.0" >> app.txt
git add . && git commit -m "chore: add version info"

# Go back to your branch and pull in the latest main
git checkout feature/add-greeting
git merge main
```

* Here, if you are in main and merge new branch in main
* Then current means main code
* Upcoming means new branch code

### Going Back to Old Commits Revert vs Reset

* Safe Plan Revert ( will have a new commit but safe and keep history )

```
# Option A — safe undo (keeps history, good for shared branches)
git revert HEAD
# This creates a NEW commit that undoes the last one. History stays clean.
```

* Risk Way Use Revert

```
git reset --soft HEAD~1
```

* Your changes come back to unstaged. The commit disappears.
* Add --hard if you want to discard the changes completely.
* After changes, need to use `git push --force-with-lease`

> git reset --hard HEAD\~1: ⚠️ DANGEROUS. Undoes the commit AND completely wipes out all the code changes you made in that commit. Your working directory will look exactly like it did before you ever made that last commit.

### Check Changes : git diff

```
# what changes
git diff

# See exactly what changed between two commits
git diff HEAD~3 HEAD~1

# Shows the changes you have already staged and are about to commit
git diff --staged
git diff --cached

# Shows the difference between your current feature branch and the main branch.
git diff main..my-feature-branch

# Skips the code output and just lists the names of the files that changed.
git diff --name-only

```

### How to close git random Terminals

```
:wq
q
```

### Advance Git

* Suppose you have a commit done in 14 april and you daily do a commit until 22 april
* Now you saw a issue but not sure commit in came from
* instead of going back to each commit
* Use git bisect

```
# Use git bisect to find WHICH commit introduced a problem
git bisect start
git bisect bad HEAD         # current state is broken
git bisect good HEAD~4      # this older state was fine
# Git will check out commits one by one — you mark each good/bad until it finds the culprit
git bisect reset            # done, go back to HEAD
```

git bisect feels advanced but the concept is simple — it binary searches through your commit history. Even just knowing it exists will save you hours someday.
