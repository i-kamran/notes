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

- [Basic Logging](#basic-logging)
  - [Filtering Log Results](#filtering-log-results)
  - [Analyzing Commits](#analyzing-commits)
- [Viewing commits](#viewing-commits)
- [Branching](#branching)
- [Merging](#merging)
  - [Undoing Changes](#undoing-changes)
  - [Squash Merging](#squash-merging)
  - [Rebasing](#rebasing)
  - [Cherry-picking](#cherry-picking)
  - [Bringing Files From Another Branch](#bringing-files-from-another-branch)
- [Stashing](#stashing)
- [Fetching](#fetching)
- [References](#references)

<!-- tocstop -->

## Basic Logging

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
- `-v` verbose
- `-vv` to show remote branch tracking info

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

### Merge Strategies

Git offers several merge strategies for handling different scenarios:

- `recursive` (default) Strategy

  Used for typical merges between two branches. It handles conflicts by
  merging changes from both branches and attempting to resolve them automatically.

  ```sh
  git checkout main
  git merge feature/login   # Uses the recursive strategy by default
  ```

- `ours` Strategy

  Resolves conflicts by keeping the changes from the current branch and
  discarding those from the merged branch.

  ```sh
  git checkout main
  git merge feature/login -s ours
  ```

  **Use Case**: Useful when you want to mark a branch as merged but prefer
  to keep the current branch's content.

- `theirs` Strategy

  Prioritizes changes from the branch being merged and discards changes from the
  current branch.

  ```sh
  # Start a merge with the default strategy
  git checkout main
  git merge feature/login

  # Manually resolve conflicts by choosing "theirs" version
  git checkout --theirs .

  # Complete the merge
  git add .
  git commit
  ```

  **Note**: `theirs` is not a built-in strategy like `ours` and must be used
  during manual conflict resolution.

- `octopus` Strategy

  Used for merging more than two branches at once, primarily when there are no
  conflicts between them.

  ```sh
  git checkout main
  git merge feature/login feature/dashboard feature/settings -s octopus
  ```

  **Use Case**: Ideal for batch merging multiple feature branches in projects
  without conflicts.

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

## Fetching

Fetch retrieves the latest changes from a remote repository

```sh
# Fetch from default remote (origin)
git fetch

# Fetch from a specific remote
git fetch origin

# Fetch from all remotes
git fetch --all

# Fetch a specific branch
git fetch origin branch-name

# Fetch all branches
git fetch origin '+refs/heads/*:refs/heads/*'

# Fetch tags
git fetch --tags
```

- Fetching is a safe operation as it doesn't modify your local branches
- Use --depth for partial clones to reduce network transfer

```sh
# Shallow fetch with limited history
git fetch --depth 1 origin main
```

## Pulling

Git command used to update the local version of a repository from a remote. [FreeCodeCamp](https://www.freecodecamp.org/news/git-pull-explained/)
Comparing Fetch vs Pull:

- git fetch: Downloads changes without merging
- git pull: Downloads and immediately merges changes

```sh
git pull # 3 way merge by default
git pull -rebase
```

### Rebasing Strategy

- Replays local commits on top of remote branch
- Creates linear history
- Cleaner, more straightforward commit history

```sh
# Rebase local changes on top of remote branch
git pull --rebase

# Interactive rebase during pull
git pull -r --interactive
# Pull and fetch all remotes
git pull --all

# Pull without committing merged changes
git pull --no-commit

# Reject merge if local changes would be overwritten
git pull --no-ff

# Shallow pull with limited history
git pull --depth 1
```

## Pushing

Pushing is how you transfer commits from your local repository to a remote repository.

```sh
# Push options
git push --all                  # All branches
git push --tags                 # Include tags
git push -u origin branch       # Set upstream
git push --force-with-lease     # Safe force push
git push origin --delete branch # Delete remote branch
```

### Pushing tags

```sh
# Create and push tags
git tag -a v1.0 -m "Release version 1.0"
git push origin v1.0
git push --tags

# Delete tags
git tag -d v1.0                  # Local
git push origin --delete v1.0    # Remote
```

Best Practices

Avoid force pushing to shared branches
Use --force-with-lease instead of --force
Verify remote URLs and branch status
Pull before pushing to shared branches

### Troubleshooting

```sh
# Check remote URLs
git remote -v

# View branch status
git status
git branch -vv

# Fix rejected pushes
git pull --rebase
git push

# Update remote tracking
git remote update
```

## Rewriting History

Rewriting history in Git is essential for maintaining a clean, understandable
project history. It is often needed to modify commit history to fix mistakes,
combine related changes, or ensure the commit log tells a clear story
of project development.

### Undoing Commits

To undo local changes (not pushed to remote repository) use `reset` command.
There are 3 options to control the reset

- `--soft` removes the commit but keeps the changes staged and in the working directory.
- `--mixed` removes the commit and the staged changes. Mixed is a default option.
- `--hard` removes all changes

```sh
git reset --soft HEAD~1
```

Use `revert` to undo changes in remote repositories.
`--no-commit` option allows to revert changes across a number of commits, without
creating commits for every reversed change.

```sh
git revert --no-commit HEAD~3..HEAD   # Will revert the last 3 commits or use HEAD~3..
```

```sh
# Modify last commit
git commit --amend                 # Change message or add files
git commit --amend --no-edit       # Keep message, add files
git commit --amend --reset-author  # Update author

# Change multiple commits
git rebase -i HEAD~n
git commit --amend --author="Name <email>" --no-edit
git rebase --continue
```

## References

<!-- TODO: add notes on the following merge strategies -->

<!-- git remote prune origin -->
