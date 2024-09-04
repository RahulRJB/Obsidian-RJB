
# GIT


DATE:  24-05-24


Tags:

# References:

https://gemini.google.com/app/690123ddb68853de




# Content:


### Staging Area:
- Working dir --> Staging area![[Attachments/Pasted image 20240904225023.png]]
- `git commit -m '<msg>'` -  Staging area --> Local repo
- `git diff` -  Diff between working directory and staging area.
- `git status -v -v` -  Detailed status, all the changes made to all the files.
- `git diff --staged` -  Diff between staging area and commit version.
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

- `git checkout <branch-name>`  -  Change branch
- `git checkout -b <new-branch-name> <existing-branch-name>`  -  Creating a new branch



### git  push:

- `git push <remote-name> <local-branch-name>:<remote-branch-name>` -  pushing to a specific remote branch
- `git push -u <remote-name> <remote-branch-name>` - setting the default upstream remote branch for the current local branch.
- 

### git log:
- gives info about the various commits history.
  
- `git log --pretty=oneline <filename>` -  Used to check commit history of a particular file
- `git checkout <commit-hash> -- <filename>` -  Override the current version of the file with version at that commit.
- `git checkout HEAD~1 -- <filename>` -  Restore the file to the previous commit.
- `git show <commit-hash>:<filename>` -  Display the content of the file at that specified commit. without changing the working directory.


### git stash:
- https://www.atlassian.com/git/tutorials/saving-changes/git-stash#:~:text=The%20git%20stash%20command%20takes,for%20commit%3A%20modified%3A%20index.
- By default, git stash will stash:
	- changes that have been added to your index (staged changes)
	- changes made to files that are currently tracked by Git (unstaged changes)
  But it will not stash:
	- new files in your working copy that have not yet been staged
	- files that have been ignored
	`-u` option (or --include-untracked) tells git stash to also stash your untracked files.
	`-a` option (or --all) tells git stash to include and stash changes to ignored files.

- `git stash -u`  -  temporarily shelves (or stashes) changes made in the working copy so you can work on something else, and then come back and re-apply them later on. Stashing is handy if you need to quickly switch context and work on something else, but you're mid-way through a code change and aren't quite ready to commit.
- `git stash pop`  -  reapply previously stashed changes. removes the changes from your stash
- `git stash apply`  -  reapply the changes to your working copy and keep them in your stash

- Can create multiple stashes. `git stash list` to view them. Stashes are identified as a "WIP" – work in progress – on top of the branch and commit that you created the stash from. After a while it can be difficult to remember what each stash contains.
  To provide a bit more context, it's good practice to annotate your stashes with a description, using `git stash save "message"`
	$ git stash save "add style to our site"
		Saved working directory and index state On main: add style to our site
		HEAD is now at 5002d47 our new homepage
	
	$ git stash list
		stash@{0}: On main: add style to our site
		stash@{1}: WIP on main: 5002d47 our new homepage
		stash@{2}: WIP on main: 5002d47 our new homepage
  
  By default, `git stash pop` will re-apply the most recently created stash: stash@{0}
  You can choose which stash to re-apply by passing its identifier as the last argument, for example: `git stash pop stash@{2}`

- `git stash show`  -  To view a summary of a stash:
	  	*index.html | 1 +*
		*style.css | 3 +++*
		*2 files changed, 4 insertions(+)*
- `git stash show -p`  -  to view the full diff of a stash:
		*diff --git a/style.css b/style.css*
		*new file mode 100644*
		*index 0000000..d92368b*
		*--- /dev/null*
		*+++ b/style.css*
		*@@ -0,0 +1,3 @@*
		*+* {*
		*+  text-decoration: blink;*
		*+}*
		*diff --git a/index.html b/index.html*
		*index 9daeafb..ebdcbd2 100644*
		*--- a/index.html*
		*+++ b/index.html*
		*@@ -1 +1,2 @@*
		*+<link rel="stylesheet" href="style.css"/>*

- `git stash -p`  -  Can choose to stash just a single file, a collection of files, or individual changes from within files. If you pass the -p option (or --patch) to , it will iterate through each changed "hunk" in your working copy and ask whether you wish to stash it:
	- / : search for a hunk by regex
	- ? : help
	- n : don't stash this hunk
	- q : quit (any hunks that have already been selected will be stashed)
	- s : split this hunk into smaller hunks
	- y : stash this hunk
- `git stash branch add-stylesheet stash@{1}`  -  You can create a new branch to apply your stashed changes to. This checks out a new branch based on the commit that you created your stash from, and then pops your stashed changes onto it.
- `git stash drop stash@{1}`  -  Delete a stash


### Upstream branch:
- `git branch -vv`  -  To check all the upstream branches associated with your local branches.
- `git push -u <upstreammbranch>` -  If there's no upstream branch created for your local branch, this command used to create an upstream branch with the given name.
- `git branch --unset-upstream` -  If there is an upstream branch associated with your local branch, this command can remove it.






