---
id: git-basics
aliases:
  - elementary git
tags:
  - git
creation date: "24-10-22"
summary: Notes on git basics
---

# git-basics

<!-- toc -->

- [Basic Log Commands](#basic-log-commands)
  - [Filtering Log Results](#filtering-log-results)
- [Viewing commits](#viewing-commits)
- [References](#references)

<!-- tocstop -->

## Basic Log Commands

To view commit history use git log command

```sh
git log
```

One of the interesting flags is `--oneline` it will show a short summary of commands.

### Filtering Log Results

- To view last `n` commits just add `n` argument `git log --oneline 3`.
  This will show the last 3 commits

- To filter by author name use `--author="authorName"` flag

- To filter by date use `--after="2020-05-17`, `--before="one week ago`, `--before="yesterday"`.
  It is possible to use relative dates with this flag.

- Another **interesting** flag is `--grep` which allows to filter based on
  a commit message.

  ```sh
  git log --oneline --grep="GUI"
  ```

  Remember that this command is case sensitive.

- To search the contents use `S"hello()"` command.

  ```sh
  git log --oneline -S"hello()" --patch
  ```

  `--patch` will show what changes were made in the commit.

### Analyzing Commits

- To find contributors use `shortlog` command
- To view log across branches use the following command

  > ```sh
  > git log master..bugfix-signup-form
  > ```

  This command will show commits that are present in bugfix but not in master branch.

## Viewing commits

To view a commit use `show` command with commit id or
use `HEAD~n` to view nth commit back from the HEAD

```sh
# Show the most recent commit
git show HEAD

# Show specific commit using commit hash
git show abc123def456

# Navigate commits relative to HEAD
git show HEAD~1    # Show second most recent commit
git show HEAD~2    # Show third most recent commit
git show HEAD~n    # Show nth commit back from HEAD

# Alternative syntax using caret notation
git show HEAD^     # Same as HEAD~1
git show HEAD^^    # Same as HEAD~2
```

Show command support a number of flags

- `--name-only` - Show the changed files without content
- `--stats` - Show statistics of changes
- `HEAD~n:path/to/file.txt` - Show specific file from a commit
- `--pretty=format: HEAD~n` - Format commit

`show` command only displays only differences.
To view all the files in the commit use `tree-ls`.

```sh
git ls-tree HEAD              # List contents of current commit
git ls-tree master           # List contents of master branch
git ls-tree HEAD~1          # List contents of previous commit

# Show specific directory
git ls-tree HEAD:path/to/dir
```

## Branching

To create a branch use `branch` command.
Branch supports a number of flags

- `-m` to move a branch with reflogs
- `-c` to copy a branch with reflogs
- `-a` to list all branches (local and remote)
- `-d` delete fully merged branch

To change branches use `switch`

```sh
git switch bugfix-signup-form
```

To create and switch to a branch use `-C` option with `swith` command.

## Merging

To merge branches, use the `merge` command.
`merge` supports a number of useful flags:

- `--no-ff` to disable fast-forward merging, creating a merge commit even when
  the merge could be fast-forwarded.
- `--squash` to combine all changes from the branch being merged into a single commit.
- `--abort` to cancel an in-progress merge, reverting to the pre-merge state.
- `--no-commit` to prevent an automatic commit after a successful merge, allowing
  you to review changes before committing.

To perform a merge, specify the branch you want to merge into the current branch:

```sh
git merge feature/login
```

### Undoing Changes

To undo a commit or merge commit, you can use either the `reset` or `revert` command:

- **`reset`**: Moves the branch pointer back to a specific commit, removing all
  commits after that point from history. There are three options for `reset`:

  - `--soft` keeps changes in the staging area.
  - `--mixed` (default) keeps changes in the working directory but removes them
    from staging.
  - `--hard` removes changes entirely (use with caution as this cannot be undone).

  ```sh
  # Undo the last commit but keep changes staged
  git reset --soft HEAD~1
  ```

- **`revert`**: Creates a new commit that undoes the changes introduced
  by a previous commit, preserving history. This is safer for shared branches
  since it does not rewrite history.
  To revert a merge commit, you need to use the `-m` flag to specify the parent
  of the commit you want to keep. The `-m` option accepts either `1` or `2`,
  depending on which parent branch you want to preserve:

  - `1`: Keeps the first parent (usually the branch you were on when the merge occurred).
  - `2`: Keeps the second parent (the branch that was merged in).

  ```sh
  # Revert a specific commit by hash
  git revert <commit-hash>

  # Revert merge commit
  git revert -m 1 HEAD # or use <merge-commit-hash>
  ```

### Squash Merging

Squash merging combines all commits from a feature branch into a single commit
when merging into the target branch. This creates a cleaner, more manageable history.

```sh
# Using merge --squash
git merge --squash feature-branch
git commit -m "Squashed feature branch commits"
```

### Rebasing

Rebasing is an alternative to merging that rewrites commit history by moving or
combining a sequence of commits to a new base commit. This creates a linear
project history.

```sh
# While on feature branch
git rebase main

# Or specify the branch explicitly
git rebase main feature-branch
# Rebase last 3 commits interactively
git rebase -i HEAD~3
```

Common interactive rebase commands:

- pick: Keep the commit as is
- reword: Change the commit message
- edit: Stop to amend the commit
- squash: Combine with previous commit
- fixup: Like squash, but discard the commit message
- drop: Remove the commit

### Cherry-picking

Cherry-pick is similar to rebase but works with individual commits

```sh
# Apply a specific commit to current branch
git cherry-pick <commit-hash>

# Cherry-pick without committing
git cherry-pick -n <commit-hash>
```

### Bringing Files From Another Branch

Use `git restore` with `--source=` flag

```sh
git restore with --source=feature-branch -- file.txt
```

## Stashing

Before switching to a different branch, ensure all changes are either committed
or stashed. To stash use the following command

```sh
git stash push -a -m "New features"
```

`-a` or `--all` options are used to stash untracked files

## References
