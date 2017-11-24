# Git Notes

## Create a branch for an incoming PR
```shell
git fetch origin pull/[pull ID]/head:[a branch name]
```

And then you can checkout that branch
```shell
git checkout [a branch name]
```

## Managing branches

### Delete a Remote branch or Tag
```shell
git push -d [REMOTE] [BRANCH]
```

### Delete a Local branch
```shell
git branch -d [BRANCH]
```
If you have a local branch that still needs to be merged to a tracking remote, then you cannot delete it unless you `--force` it.
A shortcut to specify both `--delete` and `--force` is to use a capital D.
```shell
git branch -D [BRANCH]
```

### Delete a Local Tag
```shell
git tag --delete [TAG]
```

## Managing Remotes

### Adding a remote
```shell
git remote add [REMOTE NAME] [URL]
```

### Renaming a remote
```shell
git remote rename [OLD NAME] [NEW NAME]
```

## Using stash

### stash all changes
```shell
git stash
```

### listing your stashes
```shell
git stash list
```

### show a diff of a stash
```shell
git stash show -p stash@{0}
```
