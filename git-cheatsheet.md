# Git Command Cheat Sheet

## Setup and Configuration

### Initial Setup
```bash
# Configure user information globally
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Set default editor
git config --global core.editor "vim"

# Configure line ending preferences
git config --global core.autocrlf input    # For macOS/Linux
git config --global core.autocrlf true     # For Windows

# Set up colorful output
git config --global color.ui auto

# Create global .gitignore
git config --global core.excludesfile ~/.gitignore_global
```

### Configuration Management
```bash
# List all configurations
git config --list

# List global configurations
git config --global --list

# Get specific configuration value
git config user.name

# Edit global configuration file directly
git config --global --edit
```

## Repository Setup

### Creating Repositories
```bash
# Initialize a new repository
git init

# Clone an existing repository
git clone https://github.com/username/repository.git

# Clone to a specific folder
git clone https://github.com/username/repository.git folder-name

# Clone a specific branch
git clone -b branch-name https://github.com/username/repository.git

# Create a bare repository (for servers)
git init --bare
```

## Basic Workflow

### Status and Information
```bash
# Check repository status
git status

# Show shortened status
git status -s

# Display commit history
git log

# Show concise commit history (one line per commit)
git log --oneline

# Show commit history with graph
git log --graph

# Show commit history for specific file
git log -- filename

# Show changes in commit
git show commit-hash
```

### Staging Changes
```bash
# Add file to staging area
git add filename

# Add all files to staging area
git add .

# Add only modified and deleted files (not untracked)
git add -u

# Add parts of files interactively
git add -p

# Remove file from staging area (keep the file)
git restore --staged filename    # Git 2.23+
git reset HEAD filename          # older Git versions
```

### Committing Changes
```bash
# Commit staged changes
git commit -m "Commit message"

# Add all tracked files and commit in one step
git commit -am "Commit message"

# Amend the last commit (add forgotten changes)
git commit --amend

# Amend the last commit with new message
git commit --amend -m "New commit message"
```

### Undoing Changes
```bash
# Discard changes in working directory
git restore filename              # Git 2.23+
git checkout -- filename          # older Git versions

# Revert specific commit (creates new commit)
git revert commit-hash

# Reset to specific commit (moves HEAD, dangerous!)
git reset commit-hash             # keeps changes uncommitted (mixed mode)
git reset --soft commit-hash      # keeps changes staged
git reset --hard commit-hash      # discards all changes (DANGER!)
```

## Branching and Merging

### Branch Management
```bash
# List all branches
git branch

# List remote branches
git branch -r

# List all branches (local and remote)
git branch -a

# Create new branch
git branch branch-name

# Create and switch to new branch
git checkout -b branch-name       # older Git versions
git switch -c branch-name         # Git 2.23+

# Switch to existing branch
git checkout branch-name          # older Git versions
git switch branch-name            # Git 2.23+

# Rename branch
git branch -m old-name new-name

# Delete branch
git branch -d branch-name         # safe delete (checks if merged)
git branch -D branch-name         # force delete
```

### Merging
```bash
# Merge branch into current branch
git merge branch-name

# Merge without automatic commit
git merge --no-commit branch-name

# Abort a merge with conflicts
git merge --abort

# Continue merge after resolving conflicts
git merge --continue
```

### Rebasing
```bash
# Rebase current branch onto another branch
git rebase branch-name

# Interactive rebase for editing commits
git rebase -i branch-name
git rebase -i HEAD~3              # Rebase last 3 commits

# Continue rebase after resolving conflicts
git rebase --continue

# Abort rebase
git rebase --abort
```

## Remote Repositories

### Remote Management
```bash
# List remote repositories
git remote -v

# Add remote repository
git remote add name url

# Remove remote
git remote remove name

# Rename remote
git remote rename old-name new-name

# Change remote URL
git remote set-url name new-url

# Show information about a remote
git remote show origin
```

### Fetching and Pulling
```bash
# Fetch changes from remote
git fetch remote-name

# Fetch all remotes
git fetch --all

# Pull changes from remote (fetch + merge)
git pull remote-name branch-name

# Pull with rebase instead of merge
git pull --rebase remote-name branch-name
```

### Pushing
```bash
# Push changes to remote
git push remote-name branch-name

# Push all branches to remote
git push remote-name --all

# Push tags to remote
git push remote-name --tags

# Force push (DANGER! overwrites remote history)
git push --force remote-name branch-name

# Safer force push (checks if anyone else updated)
git push --force-with-lease remote-name branch-name

# Push and set upstream
git push -u remote-name branch-name
```

### Tracking Branches
```bash
# Set upstream branch
git branch --set-upstream-to=remote-name/branch-name

# Create a branch that tracks remote branch
git checkout -b branch-name remote-name/branch-name
git checkout --track remote-name/branch-name
```

## Working with Changes

### Stashing
```bash
# Stash changes
git stash

# Stash with message
git stash save "Work in progress"

# Include untracked files in stash
git stash -u

# List stashes
git stash list

# Apply most recent stash
git stash apply

# Apply specific stash
git stash apply stash@{2}

# Apply and remove stash
git stash pop

# Remove most recent stash
git stash drop

# Remove specific stash
git stash drop stash@{2}

# Clear all stashes
git stash clear
```

### Viewing Differences
```bash
# Show unstaged changes
git diff

# Show staged changes
git diff --staged

# Show changes between two commits
git diff commit1..commit2

# Show changes between branches
git diff branch1..branch2

# Show changes in specific file
git diff -- filename

# Show word by word differences
git diff --word-diff
```

### Viewing History
```bash
# Show commit history with patches
git log -p

# Show statistics summary
git log --stat

# Show history with graph, branch, and decorations
git log --graph --decorate --all

# Show history of specific author
git log --author="Name"

# Show history with date range
git log --since="2 weeks ago" --until="yesterday"

# Search in commit messages
git log --grep="keyword"

# Search for code changes
git log -S"code_string"

# Show commit history for file (including renames)
git log --follow filename
```

## Tags

### Tag Management
```bash
# List tags
git tag

# List tags matching pattern
git tag -l "v1.8.5*"

# Create lightweight tag
git tag tag-name

# Create annotated tag
git tag -a tag-name -m "Tag message"

# Create tag for specific commit
git tag -a tag-name commit-hash

# Show tag information
git show tag-name

# Delete tag
git tag -d tag-name
```

### Sharing Tags
```bash
# Push specific tag
git push remote-name tag-name

# Push all tags
git push remote-name --tags

# Delete remote tag
git push remote-name --delete tag-name
git push remote-name :refs/tags/tag-name
```

## Advanced Operations

### Cherry-picking
```bash
# Apply commit from another branch to current branch
git cherry-pick commit-hash

# Cherry-pick without committing
git cherry-pick --no-commit commit-hash

# Cherry-pick multiple commits
git cherry-pick commit1 commit2 commit3

# Continue cherry-pick after resolving conflicts
git cherry-pick --continue

# Abort cherry-pick
git cherry-pick --abort
```

### Interactive Rebase
```bash
# Start interactive rebase
git rebase -i HEAD~5         # Last 5 commits
git rebase -i branch-name

# Available commands in interactive rebase:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# d, drop = remove commit
# x, exec = run command using shell
```

### Reflog - Recovery
```bash
# Show reference logs
git reflog

# Recover deleted branch
git checkout -b branch-name commit-hash

# Recover after hard reset
git reset --hard HEAD@{1}    # Go back one reflog entry
```

### Submodules
```bash
# Add submodule to repository
git submodule add https://github.com/username/repo.git path/to/submodule

# Initialize submodules after cloning
git submodule init

# Update submodules to their latest commits
git submodule update

# Clone repository with submodules
git clone --recurse-submodules https://github.com/username/repo.git

# Update all submodules
git submodule update --remote
```

### Worktrees
```bash
# Add a worktree
git worktree add ../path branch-name

# List worktrees
git worktree list

# Remove a worktree
git worktree remove ../path
```

### Bisect (Bug Hunting)
```bash
# Start bisect session
git bisect start

# Mark current commit as bad
git bisect bad

# Mark a known good commit
git bisect good commit-hash

# Automatic run command for each step
git bisect run ./test-script.sh

# End bisect session
git bisect reset
```

## Cleaning and Maintenance

### Cleaning
```bash
# Show what would be deleted
git clean -n

# Delete untracked files
git clean -f

# Delete untracked files and directories
git clean -fd

# Delete untracked and ignored files
git clean -fx
```

### Repository Maintenance
```bash
# Check repository integrity
git fsck

# Optimize repository
git gc

# Aggressively optimize repository
git gc --aggressive

# Prune unreachable objects
git prune

# Verify all objects in database
git fsck --full
```

## Git Hooks

### Common Hooks
```bash
# Client-side hooks (located in .git/hooks/):
pre-commit          # Run before commit
prepare-commit-msg  # Prepare default commit message
commit-msg          # Validate commit message
post-commit         # Run after commit
pre-rebase          # Run before rebase
post-rewrite        # Run after rebase/amend/etc.
pre-push            # Run before push

# Server-side hooks:
pre-receive         # Before accepting pushes
update              # During push, per branch
post-receive        # After entire push process
```

## Git Aliases and Productivity

### Setting Aliases
```bash
# Set global alias
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status

# Create more complex aliases
git config --global alias.logline "log --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"
```

### Useful Aliases
```bash
# Short status
git config --global alias.s "status -s"

# Last commit
git config --global alias.last "log -1 HEAD"

# Visual log
git config --global alias.visual "log --graph --decorate --oneline --all"

# Unstage files
git config --global alias.unstage "reset HEAD --"

# Show all branches with last commit
git config --global alias.branches "for-each-ref --sort=-committerdate --format='%(color:red)%(objectname:short)%(color:reset) %(color:yellow)%(refname:short)%(color:reset) (%(color:green)%(committerdate:relative)%(color:reset)) %(contents:subject)' refs/heads"
```

## Git Workflows

### Feature Branch Workflow
```bash
# Create feature branch
git checkout -b feature/new-feature

# Work and commit changes
git add .
git commit -m "Add new feature"

# Keep branch updated with main
git checkout main
git pull
git checkout feature/new-feature
git rebase main

# Push feature branch
git push -u origin feature/new-feature

# Create Pull Request (using web interface)

# After PR is merged, clean up
git checkout main
git pull
git branch -d feature/new-feature
```

### Gitflow Workflow
```bash
# Initialize gitflow
git flow init

# Start feature
git flow feature start new-feature

# Finish feature
git flow feature finish new-feature

# Start release
git flow release start 1.0.0

# Finish release
git flow release finish 1.0.0

# Start hotfix
git flow hotfix start hotfix-name

# Finish hotfix
git flow hotfix finish hotfix-name
```

### Trunk-Based Development
```bash
# Pull latest
git checkout main
git pull

# Create short-lived feature branch
git checkout -b feature-xyz

# Work and commit changes
git add .
git commit -m "Implement feature xyz"

# Keep updated with main
git checkout main
git pull
git checkout feature-xyz
git rebase main

# Push changes
git push origin feature-xyz

# After code review, merge to main
git checkout main
git merge --ff-only feature-xyz
git push
git branch -d feature-xyz
```

## Common Git Patterns

### Splitting Commits
```bash
# Start interactive rebase
git rebase -i HEAD~3

# Mark commits as "edit"
# For each commit marked as "edit":
git reset HEAD^          # Unstage changes
git add -p               # Add changes in chunks
git commit -m "First part"
git add .                # Add remaining changes
git commit -m "Second part"
git rebase --continue    # Continue to next commit
```

### Squashing Commits
```bash
# Squash last 3 commits
git rebase -i HEAD~3

# In editor, change "pick" to "squash" or "s" for commits to squash
# Write new commit message
```

### Safely Merging Someone Else's PR
```bash
# Add their repository as remote
git remote add contributor https://github.com/contributor/repo.git

# Fetch their changes
git fetch contributor

# Create a local branch for their PR
git checkout -b contributor-feature contributor/feature-branch

# Test their changes

# Merge into your branch if everything works
git checkout main
git merge contributor-feature
```

### Finding Bugs with Bisect
```bash
# Start the process
git bisect start

# Mark current version as bad
git bisect bad

# Mark a known good commit
git bisect good v1.0

# Git will checkout middle commit to test
# After testing, mark commit as good or bad
git bisect good  # or
git bisect bad

# Continue until git finds the first bad commit
# When finished:
git bisect reset
```

## Advanced Tips and Tricks

### Temporarily Switch to Another Branch
```bash
# Save current work
git stash

# Switch branches
git checkout other-branch

# Do work, then come back
git checkout original-branch

# Get your work back
git stash pop
```

### Fix Last Commit
```bash
# Change commit message
git commit --amend -m "New message"

# Add forgotten files to last commit
git add forgotten-file
git commit --amend --no-edit
```

### Save Typing with Push Default
```bash
# Set push.default to push current branch to matching remote
git config --global push.default current
```

### Skip Staging Area
```bash
# Commit tracked files directly (skips staging)
git commit -a -m "Skip staging area"
```

### Search Entire Git History
```bash
# Search through all text in all commits
git grep "search-term" $(git rev-list --all)
```

### Find Out Who Changed a Line
```bash
# See who changed each line and in which commit
git blame filename

# Show more context
git blame -C -L 10,20 filename
```

### Preserve Working Directory While Switching Branches
```bash
# Stash changes including untracked files
git stash -u

# Switch branches
git checkout other-branch

# Come back
git checkout original-branch
git stash pop
```

### Create an Empty Branch
```bash
# Create branch with no parent
git checkout --orphan new-branch

# Remove all files from staging
git rm -rf .

# Now you can add your files and commit
```

### Sign Your Commits
```bash
# Configure GPG key
git config --global user.signingkey YourGPGKeyID

# Sign a commit
git commit -S -m "Signed commit message"

# Configure Git to sign all commits
git config --global commit.gpgsign true
```

## Git Version Comparison
```bash
# Modern Git commands (Git 2.23+)
git switch branch-name       # vs git checkout branch-name
git restore file             # vs git checkout -- file
git restore --staged file    # vs git reset HEAD file
```
