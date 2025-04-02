# Advanced Git: Cherry-Picking and Merging Strategies

## Advanced Cherry-Picking

Cherry-picking is a powerful Git operation that allows you to selectively apply individual commits from one branch to another. While basic cherry-picking is straightforward, mastering its advanced capabilities can significantly enhance your Git workflow.

### Cherry-Pick Fundamentals and Advanced Usage

```bash
# Basic cherry-pick
git cherry-pick <commit-hash>

# Cherry-pick without committing (leaves changes staged)
git cherry-pick --no-commit <commit-hash>

# Cherry-pick and edit the commit message
git cherry-pick --edit <commit-hash>

# Cherry-pick a merge commit (requires specifying a parent)
git cherry-pick -m 1 <merge-commit-hash>
# -m 1 typically selects the mainline (the branch you merged into)
# -m 2 selects the branch that was merged in

# Cherry-pick a range of commits (exclusive of first commit)
git cherry-pick <start-commit>..<end-commit>

# Cherry-pick a range of commits (inclusive of first commit)
git cherry-pick <start-commit>^..<end-commit>

# Cherry-pick a commit and record the origin in commit message
git cherry-pick --x <commit-hash>

# Cherry-pick preserving authorship information
git cherry-pick --signoff <commit-hash>

# Apply only the code changes, not the authorship (for patches)
git cherry-pick --strategy=recursive -X theirs <commit-hash>
```

### Complex Cherry-Pick Operations

```bash
# Cherry-pick multiple non-contiguous commits
git cherry-pick <commit1> <commit2> <commit3>

# Skip a failing cherry-pick in a sequence
git cherry-pick --skip

# Cherry-pick from another repository
git fetch <remote-repo-url> <branch>
git cherry-pick FETCH_HEAD

# Cherry-pick only changes to specific files
git cherry-pick -n <commit-hash>  # --no-commit
git restore --staged -- .         # Unstage all changes
git add path/to/file1 path/to/file2  # Add only desired files
git commit -m "Cherry-picked specific files from <commit-hash>"

# Cherry-pick only part of a commit using patch mode
git cherry-pick -n <commit-hash>
git reset HEAD
git add -p
git commit -m "Partial cherry-pick from <commit-hash>"

# Custom cherry-pick reflog entry for easier tracking
git cherry-pick --reflog-comment="cherry-pick: feature X" <commit-hash>
```

### Conflict Resolution Strategies for Cherry-Picking

```bash
# When cherry-pick conflicts occur:

# Option 1: Manually resolve, then continue
# (After fixing conflicts)
git add <resolved-files>
git cherry-pick --continue

# Option 2: Keep your version for all conflicts
git cherry-pick --strategy-option=ours <commit-hash>

# Option 3: Keep their version for all conflicts
git cherry-pick --strategy-option=theirs <commit-hash>

# Option 4: Abort the cherry-pick
git cherry-pick --abort

# For specific file conflict resolution:
git checkout --ours -- path/to/file    # Keep your version
git checkout --theirs -- path/to/file  # Take their version
git add path/to/file
git cherry-pick --continue
```

### Automated Cherry-Picking Scripts

```bash
# Example script to cherry-pick all commits containing a keyword
#!/bin/bash
for commit in $(git log --grep="FEATURE-123" --format="%H" branch-name); do
    echo "Cherry-picking $commit"
    git cherry-pick -x $commit || {
        echo "Conflict with $commit. Resolve and run 'git cherry-pick --continue'"
        exit 1
    }
done

# Example script to cherry-pick all commits affecting a specific file
#!/bin/bash
for commit in $(git log --format="%H" -- path/to/important/file); do
    echo "Cherry-picking $commit"
    git cherry-pick -x $commit || {
        echo "Conflict with $commit. Resolve and run 'git cherry-pick --continue'"
        exit 1
    }
done
```

### Cherry-Pick Best Practices

1. **Record origin**: Always use `-x` flag for auditing and traceability
2. **Targeted picks**: Pick only what you need, not entire branches
3. **Test after complex operations**: Cherry-picking many commits may introduce subtle integration issues
4. **Watch for dependent commits**: Cherry-picking a commit might require its predecessors for context
5. **Document extensively**: Note in your PR/MR what was cherry-picked and why
6. **Beware of merge commits**: Cherry-picking merge commits requires careful parent selection

## Advanced Merging Strategies

Git offers multiple merging strategies and options that go well beyond the basic `git merge` command. Understanding these strategies can help you handle complex merging scenarios more effectively.

### Git Merge Strategies Overview

```bash
# Recursive strategy (default) - handles multiple merge bases
git merge --strategy=recursive branch-name

# Resolve strategy - simpler three-way merge
git merge --strategy=resolve branch-name

# Octopus strategy - for merging more than two branches
git merge --strategy=octopus branch1 branch2 branch3

# Ours strategy - ignore all changes from other branch (just mark as merged)
git merge --strategy=ours branch-name

# Subtree strategy - specialized for subtree merges
git merge --strategy=subtree --subtree=path/to/dir branch-name
```

### Recursive Strategy Options

```bash
# Prefer our version in conflicts (current branch)
git merge -X ours branch-name

# Prefer their version in conflicts (branch being merged)
git merge -X theirs branch-name

# Ignore all whitespace changes
git merge -X ignore-all-space branch-name

# Ignore whitespace at line ends
git merge -X ignore-space-at-eol branch-name

# Treat all files as text (never as binary)
git merge -X diff-algorithm=patience branch-name

# Try harder to find a minimal diff
git merge -X diff-algorithm=histogram branch-name

# Find renames with greater similarity threshold (0-100)
git merge -X rename-threshold=75 branch-name
```

### Advanced Merge Scenarios

```bash
# Squash merge (combine all commits into one)
git merge --squash branch-name
git commit -m "Merge branch 'branch-name' with squash"

# Fast-forward only (fail if not possible)
git merge --ff-only branch-name

# No fast-forward (always create merge commit)
git merge --no-ff branch-name

# Merge with unrelated histories (e.g., joining independent projects)
git merge --allow-unrelated-histories branch-name

# Perform a "stitching" merge
git merge -s recursive -X subtree=path/to/dir branch-name

# Merge and specify custom commit message
git merge -m "Custom merge message" branch-name

# Verify signatures on commits being merged
git merge --verify-signatures branch-name

# Abort in-progress merge
git merge --abort

# Continue merge after resolving conflicts
git merge --continue  # Git 2.20+
```

### Merge Conflict Resolution Techniques

```bash
# List all conflicted files
git diff --name-only --diff-filter=U

# Use a graphical merge tool
git mergetool

# Use a specific merge tool
git mergetool -t vimdiff
git mergetool -t kdiff3
git mergetool -t meld

# After manually resolving conflicts
git add <resolved-files>
git merge --continue

# Keep specific version of a file
git checkout --ours -- path/to/file   # Keep current branch version
git checkout --theirs -- path/to/file  # Keep merged branch version
git add path/to/file

# Get help visualizing the conflict
git log --graph --oneline --all
```

### Programmatic Conflict Resolution

```bash
# Example of a custom merge driver in .git/config:
[merge "custom-merger"]
    name = "Custom merge driver for special files"
    driver = "custom-merge-script.sh %O %A %B %P"

# In .gitattributes:
special.file merge=custom-merger

# Example custom merge script (custom-merge-script.sh):
#!/bin/bash
# $1 = base version
# $2 = our version
# $3 = their version
# $4 = path to file
# Custom merge logic here...
```

### Deferred Merge Strategies

```bash
# Create a merge commit but leave conflicts unresolved (for later)
git merge --no-commit --no-ff branch-name
# If there are conflicts:
git diff  # Review what conflicts will need resolution later
git merge --abort
git notes add -m "Deferred merge with conflicts in files X, Y, Z"

# Later, when ready to resolve:
git merge branch-name  # Retry the merge
```

### Merge-Rebase Hybrid Approaches

```bash
# Rebase feature branch onto latest main, then merge with no-ff
git checkout feature
git rebase main
git checkout main
git merge --no-ff feature

# Alternative: Rebase all commits except merge commits
git rebase --rebase-merges main

# Preserve uncommitted changes during complex merges
git stash
git merge branch-name
git stash pop
```

### Complex Branch Topology Management

```bash
# Grafting branches with custom history (advanced)
git replace --graft <commit-to-replace> <parent-commit>
git filter-branch -- --all  # Make permanent

# Creating a synthetic merge base for unrelated branches
git merge-base --octopus branch1 branch2 branch3

# Finding best common ancestor for a merge
git merge-base --all branch1 branch2

# Simulating merge to see what will happen
git merge --no-commit --no-ff branch-name
# Inspect result
git merge --abort
```

### Merge Metrics and Analysis

```bash
# Count lines changed by a merge
git diff --stat HEAD HEAD^1  # First parent (your branch)
git diff --stat HEAD HEAD^2  # Second parent (merged branch)

# Show all merge conflicts in a repository's history
git log --merges --grep="conflict"

# Find all "evil merges" (where same lines were modified in both parents)
git log --merges -p | grep -A 10 ">>>>>"

# Show all octopus merges (merges with 3+ parents)
git log --min-parents=3 --oneline
```

## Comparative Merge Workflows

### Traditional Merge Workflow
```bash
# Feature branch development
git checkout -b feature main
# (Make changes...)
git commit -am "Feature work"

# Stay in sync with main (merge approach)
git checkout feature
git merge main  # Creates merge commit

# When complete, merge back to main
git checkout main
git merge --no-ff feature  # Preserves feature history
```

### Rebase Workflow
```bash
# Feature branch development
git checkout -b feature main
# (Make changes...)
git commit -am "Feature work"

# Stay in sync with main (rebase approach)
git checkout feature
git rebase main  # Replay commits on top of latest main

# When complete, merge to main (can fast-forward)
git checkout main
git merge feature  # Clean, linear history
```

### Hybrid Approaches
```bash
# Cherry-pick based workflow (for backporting)
git checkout release-1.0
git cherry-pick -x abc123  # Cherry-pick bugfix from main

# Rebase then merge --no-ff (GitLab style)
git checkout feature
git rebase main
git checkout main
git merge --no-ff feature  # Clean history but preserves feature branch

# Squash merging development branches
git checkout main
git merge --squash feature
git commit -m "Feature: Add new functionality X"
```

## Meta-Branch Operations

### Branch Comparison and Metrics
```bash
# Commits in branch1 that aren't in branch2
git log branch1 ^branch2 --no-merges

# Differences in files between branches
git diff --name-status branch1..branch2

# Branches containing a specific commit
git branch --contains <commit-hash>

# Common ancestor of branches (merge base)
git merge-base branch1 branch2
```

### Higher-Order Branch Management
```bash
# Selective branch pruning
git remote prune origin --dry-run  # Show what would be pruned
git remote prune origin             # Actually prune

# Rename remote branch
git push origin :old-branch-name    # Delete remote branch
git push origin local-name:new-branch-name  # Push with new name

# Check for divergence between branches
git rev-list --left-right --count branch1...branch2

# Find first common commit in two branches
git merge-base branch1 branch2
```

## Merge and Cherry-Pick Safety Protocols

### Pre-Merge/Cherry-Pick Checklist
1. Ensure working directory is clean (`git status`)
2. Update local copy of target branch (`git fetch origin target-branch`)
3. Create a backup branch (`git branch backup-name`)
4. Consider creating a test branch for the operation first

### Post-Merge/Cherry-Pick Verification
1. Check logs to confirm expected commits are included
2. Run tests to ensure functionality is preserved
3. Examine specific files known to be affected
4. Consider a `git diff` against expected state

### Recovery Patterns
```bash
# Undo a merge (if it was the last commit)
git reset --hard HEAD^

# Undo a merge (even if not the last commit)
git revert -m 1 <merge-commit-hash>

# Undo a cherry-pick
git reset --hard HEAD^   # If it was the last commit
# Or more specifically 
git reflog                   # Find pre-cherry-pick state
git reset --hard HEAD@{1}    # Go back to before cherry-pick

# Create a reference for safety before dangerous operations
git tag BEFORE_MERGE
# Later if needed
git reset --hard BEFORE_MERGE
```

## Implementation of Custom Merge and Cherry-Pick Strategies

### Example: Feature Flag Merge Strategy
A script that modifies cherry-picks or merges to automatically wrap changed code in feature flags:

```bash
#!/bin/bash
# feature-merge.sh
# Usage: git checkout target-branch && ./feature-merge.sh source-branch feature-flag-name

SOURCE_BRANCH=$1
FEATURE_FLAG=$2

# Create temporary branch
git checkout -b temp-merge-branch

# Merge the source branch
git merge --no-ff $SOURCE_BRANCH

# Find all files changed in the merge
CHANGED_FILES=$(git diff --name-only HEAD^)

# For each changed file, modify to wrap in feature flag
for file in $CHANGED_FILES; do
  if [[ $file == *.js || $file == *.ts ]]; then
    # Simple example - would need more sophisticated parsing in reality
    sed -i "s/\(.*new functionality.*\)/if (featureFlags.$FEATURE_FLAG) {\n\1\n}/" $file
    git add $file
  fi
done

# Amend the merge commit with the flagged changes
git commit --amend -m "Merge branch '$SOURCE_BRANCH' with feature flag '$FEATURE_FLAG'"

# Switch back to target branch and merge our modified branch
git checkout -
git merge --no-ff temp-merge-branch
git branch -D temp-merge-branch
```

### Example: Semantic Conflict Resolution
A strategy for resolving conflicts based on the semantic meaning of changes:

```bash
#!/bin/bash
# semantic-merge.sh
# Analyzes conflicting sections to make intelligent merge decisions

FILE_PATH=$1
MERGE_STRATEGY=$2  # e.g., "method-level", "version-precedence"

# Get conflict markers
conflicts=$(grep -n "<<<<<<< HEAD" $FILE_PATH | cut -d ":" -f 1)

for line in $conflicts; do
  start_line=$line
  marker_line=$(grep -n "=======" $FILE_PATH | awk -v sl="$start_line" '$1 > sl {print $1; exit}' | cut -d ":" -f 1)
  end_line=$(grep -n ">>>>>>>" $FILE_PATH | awk -v sl="$start_line" '$1 > sl {print $1; exit}' | cut -d ":" -f 1)
  
  our_section=$(sed -n "${start_line},$((marker_line-1))p" $FILE_PATH | tail -n +2)
  their_section=$(sed -n "$((marker_line+1)),$((end_line-1))p" $FILE_PATH)
  
  case $MERGE_STRATEGY in
    "method-level")
      # Extract method signatures and compare
      our_methods=$(echo "$our_section" | grep -E "function |def |public |private " | sort)
      their_methods=$(echo "$their_section" | grep -E "function |def |public |private " | sort)
      
      # Complex logic would go here to merge at method boundaries
      # This is a simplistic example
      if [[ "$our_methods" == "$their_methods" ]]; then
        # Methods are the same, choose newer implementation
        sed -i "${start_line},${end_line}c\\${their_section}" $FILE_PATH
      else
        # Methods differ, combine them
        combined=$(cat <(echo "$our_section") <(echo "$their_section") | sort -u)
        sed -i "${start_line},${end_line}c\\${combined}" $FILE_PATH
      fi
      ;;
      
    "version-precedence")
      # Look for version numbers and choose higher version
      our_version=$(echo "$our_section" | grep -E "version |VERSION |v[0-9]" | grep -o -E "[0-9]+\.[0-9]+\.[0-9]+")
      their_version=$(echo "$their_section" | grep -E "version |VERSION |v[0-9]" | grep -o -E "[0-9]+\.[0-9]+\.[0-9]+")
      
      if [[ -n "$our_version" && -n "$their_version" ]]; then
        if [[ $(echo "$our_version $their_version" | awk '{if ($1 > $2) print "yes"; else print "no"}') == "yes" ]]; then
          sed -i "${start_line},${end_line}c\\${our_section}" $FILE_PATH
        else
          sed -i "${start_line},${end_line}c\\${their_section}" $FILE_PATH
        fi
      fi
      ;;
      
    # Add more strategies as needed
  esac
done
```

## Multi-Repository Merge Management

### Submodule Merge Strategies
```bash
# Update all submodules before merging
git submodule update --recursive --remote

# Merge in main repo while keeping specific submodule versions
git merge -X subtree=path/to/submodule feature-branch

# Lock submodules during merge
git submodule foreach git checkout -b merge-lock-branch

# Restore after merge
git submodule foreach git checkout master
git submodule update --recursive
```

### Cross-Repository Cherry-Picking
```bash
# Add remote for another repository
git remote add other-repo https://github.com/user/other-repo.git
git fetch other-repo

# Cherry-pick from the other repository
git cherry-pick other-repo/branch~3..other-repo/branch~1

# Cherry-pick a specific file/change from another repo
git show other-repo/branch:path/to/file > path/to/file
git add path/to/file
git commit -m "Cherry-picked file from other-repo"
```

## Advanced Cases and Rare Scenarios

### Octopus Merge with Conflict Resolution
The octopus merge (merging more than two branches) normally fails on conflicts. Here's a way to handle it:

```bash
# For octopus merge with potential conflicts:
git checkout -b temp-octopus main
for branch in branch1 branch2 branch3 branch4; do
  git merge --no-ff $branch || {
    # Handle conflicts
    echo "Resolving conflicts with $branch"
    # Resolve conflicts...
    git add .
    git commit -m "Resolved conflicts with $branch"
  }
done
git checkout main
git merge --no-ff temp-octopus
git branch -d temp-octopus
```

### Time-Based Branch Recreation
Reconstructing a branch as it existed at a specific time:

```bash
# Find last commit before target time
TARGET_TIME="2023-01-15 15:30:00"
git checkout -b historical-branch
git reset --hard $(git rev-list -n 1 --before="$TARGET_TIME" branch-name)

# Apply all commits in time range to a new branch
START_TIME="2023-01-01 00:00:00"
END_TIME="2023-01-31 23:59:59"

git checkout -b time-slice-branch main
git log --reverse --format="%H" --after="$START_TIME" --before="$END_TIME" main |
while read commit; do
  git cherry-pick -x $commit || {
    git cherry-pick --abort
    echo "Skipping problematic commit $commit"
  }
done
```

### Content-Based Selective Merging
Merge only commits that touch specific patterns or code:

```bash
# Create branch for filtered merge
git checkout -b filtered-merge main

# Get all commits from feature branch
git log --format="%H" main..feature-branch |
while read commit; do
  # Check if commit modifies target files/patterns
  if git show $commit | grep -q "function updateUser"; then
    git cherry-pick -x $commit || {
      # Handle conflict
      git add .
      git cherry-pick --continue
    }
  fi
done
```

### Resurrection Merge
Bringing back a previously deleted branch:

```bash
# Find the commit where branch was deleted using reflog
git reflog --all | grep "branch-name"

# Identify the commit hash before deletion
LAST_COMMIT=$(git reflog --all | grep "branch-name" | head -n 1 | awk '{print $1}')

# Create new branch from that point
git checkout -b resurrected-branch $LAST_COMMIT

# Or if you know a commit hash that was on the branch:
git checkout -b resurrected-branch <commit-hash>
git log --graph --oneline  # Verify it has expected history
```
