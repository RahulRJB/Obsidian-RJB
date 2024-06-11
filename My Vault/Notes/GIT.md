
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


### git clone:

- Used to **download a complete copy** of an existing remote repository for the first time. It creates a new directory on your local machine containing all the files and version history of the remote repository.
- Typically used at the beginning of your involvement with a project to get a local working copy.
- Imagine `git clone` as **downloading a zip file** of a project from the internet.
- Think of `git pull` as **updating an existing project** on your computer with the latest changes from a central server.
- Basic syntax:  `git clone <repository-URL>`
- **Cloning a specific branch:** `git clone -b <branch-name> <repository-URL>`



### Create a new branch:

- `git checkout -b <new-branch-name> <existing-branch-name>`



### GIT Push:

- `git push <remote-name> <local-branch-name>:<remote-branch-name>`