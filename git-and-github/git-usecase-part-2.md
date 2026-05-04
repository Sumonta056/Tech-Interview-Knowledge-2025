# Git usecase (Part-2)

### How to Rebase?

1. Switch to your feature branch (if not already on it):

```
git checkout your-branch
```

2. Fetch the latest changes from remote:

```
git fetch origin
```

3. Rebase onto master:

```
git rebase origin/master
```

4. Resolve any conflicts that arise during the rebase.

```
git add .
git rebase --continue
git rebase -i origin/master
git rebase --abort
```

5. Force push your feature branch to remote (if needed):

```bash
git push --force-with-lease
```

Note: Use --force-with-lease instead of --force for safety. Only force push to branches you own, never to shared branches like master.

### Undo Last Commit

```bash
git reset --soft HEAD~1
```

This undoes the commit but keeps your changes staged and ready to commit again. ( Keep changes in Staged)

```bash
git reset --mixed HEAD~1
```

This undoes the commit and unstages your changes, but keeps them in your working directory. (Keep changes in Unstaged)

```bash
git reset --hard HEAD~1
```

This undoes the commit and discards all changes in your working directory. (Discard changes)

### Merge Branches into Main

```bash
git checkout main
```

```bash
git merge feature-branch
```

```bash

Option 1 - Keep the default message:

- Press Esc (to ensure you're in command mode)
- Type :wq and press Enter

Option 2 - Edit the message:

- Press i to enter insert mode
- Type your custom message at the top (before the # lines)
- Press Esc
- Type :wq and press Enter

Option 3 - Cancel the merge:

- Press Esc
- Type :q! and press Enter
```

```bash
# Push to remote
git push origin main

# Optional: Delete the branch locally
git branch -d 10-doc-create-frontend-instructions-and-rules

# Optional: Delete the branch remotely
git push origin --delete 10-doc-create-frontend-instructions-and-rules
```

```bash
# This only merges if it can be done as a fast-forward (no merge commit).
git merge --ff-only 10-doc-create-frontend-instructions-and-rules
```

```bash
# For merges without conflicts, skip the editor
git merge 10-doc-create-frontend-instructions-and-rules --no-edit
```
