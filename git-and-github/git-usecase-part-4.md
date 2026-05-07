# Git usecase (Part-4)

### Delete a branch from just local then import from origin

> Usecase : when you team mate rebase a branch that you are working its better to do this

Before you can delete a branch, you need to make sure you are not currently on it. Switch to a different branch, like `main` or `master`:

```
git checkout main
```

Force Delete: This will delete the branch regardless of its merge status. Use this if you want to completely discard your local changes.

```
git branch -D <branch_name>
```



Once the local branch is gone, you can pull down the latest version from your remote repository.

First, update your local repository with the latest information from `origin`:

```
git fetch origin
```

Next, check out the branch from the remote

```
git checkout <branch_name>
```

***

### Stage only the three files you want to keep discard rest

Stage the 3 files you want to keep

```
git add components/DnPreFetchTeaser/DnPreFetchTeaser.vue 
components/DnSegmentCollection/DnSegmentCollection.vue 
composables/useLifespanFilter.ts
```

Discard all other changes

Now, tell Git to discard all modifications to tracked files that are not in the staging area:

```
git restore .
```

Finally, move your three saved files back out of the staging area so they just show up as regular modified files in your working directory:

```
git restore --staged .
```





