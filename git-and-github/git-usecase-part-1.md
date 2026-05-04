# Git usecase (Part-1)

### Want to revert package.json and package-lock.json back to their previous (old) state in your PR.

```
git checkout origin/master -- package.json package-lock.json
```

### How to fix rebase/conflicting issues for package.lock.json?

When fixing a package-lock.json conflict during a rebase, do not try to resolve the conflict markers (<<<<, ====, >>>>) manually in a text editor. The file is too complex for human editing.

Instead, use npm to regenerate the file for you.

1. Resolve package.json first: Open package.json in your editor, fix the conflicts manually, and save the file.
2. Stage package.json:

```
git add package.json
```

3. Reset package-lock.json to the "Upstream" version: Since you are rebasing, --ours refers to the upstream branch (the one you are rebasing onto, usually main or develop). This gives you a clean, valid file to start with.

```
git checkout --ours package-lock.json
```

4. Regenerate the lockfile: Run install. This tells npm: "Take the clean upstream lockfile, look at the merged package.json, and update the lockfile to match."

```
npm install
```

5. Stage the fixed lockfile and continue:

```
git add package-lock.json
git rebase --continue
```
