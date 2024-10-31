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

## Stashing

Before switching to a different branch, ensure all changes are either committed
or stashed. To stash use the following command

```sh
git stash push -a -m "New features"
```

`-a` or `--all` options are used to stash untracked files

## References
