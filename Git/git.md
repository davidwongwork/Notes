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

### Delete a Remote branch
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

