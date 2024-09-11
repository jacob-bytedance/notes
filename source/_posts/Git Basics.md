---
title: "{{title}}"
---
I've been confused and frustrated many times by Git (or my inability to use it properly). So I've made a document summarizing my learnings and distill simple, repeatable, and effective Git recipe for dealing with different scenarios.

# Fix Small Mistakes

## Amend the Last Commit (to change the message or staged files)

```bash
git commit --amend
```

## Undo the Last Commit but Keep Changes

```bash
git reset --soft HEAD^
```
  
## Undo the Last Commit and Discard Changes

```bash
git reset --hard HEAD^
```

## Unstage Files

```bash
git reset <file>
```

## Discard All Uncommitted Changes

```bash
git reset --hard
```

# Fix Very Big Mistakes

## Reset All Commits (Including the Initial Commit)

```bash
git checkout --orphan new-branch
git rm -rf .
git commit --allow-empty -m "Initial commit"
git branch -D main  # delete the old branch
git branch -m main  # rename the new branch to main
git push --force
```