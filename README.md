# projecty
How to generate a ssh key
ssh-keygen -t rsa -b 4096 -C "your_email@example.com" 
cat ~/.ssh/id_rsa.pub

What is git revert?
git revert creates a new commit that undoes the changes introduced by a previous commit — without rewriting history.

When to Use git revert

To undo a change that was already pushed to a shared branch (like main).
To safely fix mistakes without rewriting history.
During production issues where you need to roll back one or more commits cleanly.
git revert <commit-hash>
This creates a new commit that undoes the effect of the specified commit.

Example Scenario

Suppose your commit history looks like this:

A -- B -- C -- D (main)
Now you realize that commit C introduced a bug.

Step 1: Revert commit C

git revert <commit-hash-of-C>
Git will create a new commit E:

A -- B -- C -- D -- E (main)
Commit E undoes changes from commit C.

You’ll still have commit C in history, but its changes are canceled out by E.




Git Merge vs Git Rebase
Both are used to integrate changes from one branch into another, but they work differently.

Let's say we have:

A main branch
A feature branch created from main
# Initial state
        A---B---C (feature)
       /
D---E---F (main)
1. Git Merge
Combines two branches and creates a new merge commit to preserve both histories.

Command
git checkout main
git merge feature
Resulting History
        A---B---C (feature)
       /         \
D---E---F---------M (main)
M is a new merge commit
History is non-linear
No commits are rewritten
2. Git Rebase
Moves commits from one branch onto another by replaying them.

Command
git checkout feature
git rebase main
Resulting History
D---E---F---A'---B'---C' (feature)
Now if we do:

git checkout main
git merge feature
Final result:

D---E---F---A'---B'---C' (main)
History is linear
Looks like all work happened sequentially



Workflow: Working with a Forked Repository
You fork a project, make changes in your fork, and want to sync with the original repo and submit your changes for review (usually via a pull request).

Terminology
Upstream: The original repository you forked from.
Origin: Your forked version (remote on your GitHub account).
Set Up Remotes
After forking and cloning your repo locally:

git clone https://github.com/your-username/project-name.git
cd project-name
Add the original repo as upstream:

git remote add upstream https://github.com/original-owner/project-name.git
Verify remotes:

git remote -v
You should see:

origin    https://github.com/your-username/project-name.git (fetch)
upstream  https://github.com/original-owner/project-name.git (fetch)
Fetch and Sync with Upstream
Fetch changes from upstream:
git fetch upstream
Merge into your local main branch:
git checkout main
git merge upstream/main
OR use rebase for a clean history:

git rebase upstream/main
Create a Feature Branch
Never work directly on main. Create a new branch:

git checkout -b feature/my-changes
Make your code changes, then:

git add .
git commit -m "Add feature/fix"
Push Changes to Your Fork
git push origin feature/my-changes
Create a Pull Request (PR)
Go to your fork on GitHub.
You’ll see a “Compare & pull request” button — click it.
Ensure the base repo is original repo and the base branch is main.
Add title, description, and click Create pull request.




Git Stash
git stash temporarily save changes in your working directory that are not yet committed, so you can switch branches or perform other operations without losing your work.

It’s like saying: "Hold these changes for me; I’ll come back to them later."

You're working on a new feature, but suddenly need to:

Switch to a different branch to fix a bug
Pull latest changes
Avoid committing half-done work
Rather than committing or discarding your changes, you can stash them.

1. stash your changes
git stash
This saves your uncommitted changes and reverts the working directory to the last commit.

2. List available stashes
git stash list
Example output:

stash@{0}: WIP on feature1: 1234567 Add initial layout
stash@{1}: WIP on main: abcdef0 Fix header bug
3. Apply stashed changes
git stash apply
Re-applies the latest stash but does not remove it from stash list.

To apply a specific stash:

git stash apply stash@{1}
4. Drop (delete) a stash
git stash drop stash@{0}
5. Apply and remove the stash (common case)
git stash pop
Re-applies and removes the latest stash in one step.

6. Stash only staged or only unstaged changes**
git stash -k   # Keep staged, stash only unstaged
git stash -p   # Pick interactively what to stash
7. Stash with a message
git stash save "WIP: fixing login form"
Example Use Case
You're working on feature-xyz:
git status
# modified: login.js
# modified: utils.js
Suddenly you need to switch to main to review a hotfix:
git stash
git checkout main
After you're done with main, go back:
git checkout feature-xyz
git stash pop
Now your changes are back and you're ready to continue!

Summary Table
Command	Description
git stash	Save uncommitted changes
git stash list	Show all stashes
git stash apply	Apply latest (or specific) stash
git stash pop	Apply and remove stash
git stash drop stash@{n}	Delete specific stash
git stash clear	Remove all stashes
git stash save "message"	Save with custom message




Git cherry-pick
git cherry-pick is used to apply a specific commit from one branch onto another — without merging the entire branch.

Think of it like saying: “I want just that one commit, not everything else from that branch.”

When to Use cherry-pick
You accidentally committed a fix on the wrong branch and want to copy it to the right one.
You want to apply a specific bug fix or change to multiple branches (e.g., main and release).
You don’t want to merge the full feature branch but need one important commit from it.
Basic Command
git cherry-pick <commit-hash>
This applies the changes introduced by the given commit onto your current branch.

Example Workflow
You have:

main:     A---B---C
                  \
feature:           D---E---F
Let’s say commit E is a useful fix and you want it in main.

Switch to main branch:
git checkout main
Cherry-pick the commit:
git cherry-pick <commit-hash-of-E>
Your history will look like:
main:     A---B---C---E'
                  \
feature:           D---E---F
E' is a new commit with the same changes as E, but a different hash.

Command	Description
git cherry-pick -x <hash>	Adds reference to original commit in message
git cherry-pick -n <hash>	Applies changes but doesn’t commit (stages only)
git cherry-pick --continue	After resolving conflicts
git cherry-pick --abort	Abort cherry-pick process if needed
Cherry-pick vs Merge vs Rebase
Command	Use When
merge	Want to combine full branch history
rebase	Want to replay all commits in order
cherry-pick	Want only selected commits
