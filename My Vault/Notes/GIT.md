
# GIT


DATE:  24-05-24


Tags:

# References:

https://gemini.google.com/app/690123ddb68853de




# Content:

### git pull:

- `git pull` is a combination of two commands:
    - `git fetch`: Retrieves the latest changes from the remote repository without merging them into your local branch.
    - `git merge`: Attempts to integrate the fetched changes into your current local branch.
- If there are no conflicts (changes made to the same file in both local and remote repositories), the `git pull` command merges the changes seamlessly.
- If conflicts arise, Git will halt the merge process and highlight the conflicting sections in the affected files. You'll need to resolve these conflicts manually before continuing.

### Merge conflicts:

- https://www.atlassian.com/git/tutorials/using-branches/merge-conflicts?hl=en-US
- ![[Attachments/Pasted image 20240801230326.png]]
- Git commands that can help resolve merge conflicts:
	- `git status` : The status command is in frequent use when a working with Git and during a merge it will help identify conflicted files.
	- `git log --merge`: Passing the `--merge` argument to the `git log` command will produce a log with a list of commits that conflict between the merging branches.
	- `git diff` :  `diff` helps find differences between states of a repository/files. This is useful in predicting and preventing merge conflicts.  
	- `git merge --abort` :  Executing `git merge` with the `--abort` option will exit from the merge process and return the branch to the state before the merge began.
	- `git reset` :  Can be used during a merge conflict to reset conflicted files to a know good state.


### git  merge vs git rebase:

Both `git merge` and `git rebase` are used to integrate changes from one branch into another in Git, but they achieve this in fundamentally different ways, resulting in distinct impacts on your Git history.

Differences:

**Merging (git merge):**

- **Concept:** Creates a new commit that acts as a "merge point" between your current branch and the branch you're merging from.
- **History:** Preserves the complete history of both branches, including the merge commit. This can lead to a more complex and linear Git history with additional merge commits.
- **Workflow:** Suitable for collaborative development when a clear record of integration points is valuable. It allows for easier identification of when and how branches were merged.
- **Conflicting Changes:** If there are conflicts between the branches (i.e., changes made to the same lines of code), `git merge` will pause and require you to manually resolve the conflicts before completing the merge.

**Rebasing (git rebase):**

- **Concept:** Re-writes your current branch's history to incorporate the changes from another branch.
- **History:** Creates a new linear history where your commits appear as if they were originally made on top of the other branch. This can lead to a cleaner and more streamlined Git history.
- **Workflow:** Often used for personal development or cleanup before sharing your work with others. It can be helpful for maintaining a clear and linear commit history on your branch.
- **Conflicting Changes:** Similar to merging, rebasing can encounter conflicts that need manual resolution. However, these conflicts might arise at different points in the history due to the reordering of commits.

**Choosing between Merge and Rebase:**

The choice between merge and rebase depends on your workflow and preferences. Here are some general guidelines:

- **Use merge:**
    - When collaborating with others and maintaining a clear record of integration points is important.
    - If you're unsure about potential conflicts or want a clear separation between your work and the other branch.
- **Use rebase:**
    - For personal development or cleanup before sharing your work.
    - When you want a clean and linear Git history for your branch.

### git clone:

- Used to **download a complete copy** of an existing remote repository for the first time. It creates a new directory on your local machine containing all the files and version history of the remote repository.
- Typically used at the beginning of your involvement with a project to get a local working copy.
- Imagine `git clone` as **downloading a zip file** of a project from the internet.
- Think of `git pull` as **updating an existing project** on your computer with the latest changes from a central server.
- Basic syntax:  `git clone <repository-URL>`
- **Cloning a specific branch:** `git clone -b <branch-name> <repository-URL>`



### Branching:

- `git checkout <branch-name>`        Change branch
- `git checkout -b <new-branch-name> <existing-branch-name>`



### git  push:

- `git push <remote-name> <local-branch-name>:<remote-branch-name>` -  pushing to a specific remote branch
- `git push -u <remote-name> <remote-branch-name>` - setting the default upstream remote branch for the current local branch.
- 

### git log:
- gives info about the various commits history.




