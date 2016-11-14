---
layout: post
title: Git Tips - Undoing Accidental Commits
comments: True
excerpt_separator: <!--more-->
---

Here are couple of git *undo's* to get yourself out of trouble. Before you use anything from this post, please make a copy of the your working directory and store it somewhere safe. *Git can be very unforgiving and you may lose your changes without any warning*.

### Accidentally committed to `master` instead of a new branch
Use the commands below if you have accidentally committed your changes to the master branch instead of a new branch but haven't pushed your changes to the remote repository yet.

<pre class="prettyprint lang-sh">
# Create a new branch copying current state of master
git branch new-branch
# Restore master to second to last commit. Your changes are gone from master
git reset --hard HEAD^
# Switch to the new branch to see your changes.
git checkout new-branch
</pre>

### Accidentally committed to the wrong branch
`git cherry-pick <commit-hash>` is a handy command to choose a commit from one branch and apply it to another.

<pre class="prettyprint lang-sh">
# Note the commit-hash you want to move
git log
# Restore branch to second to last commit
git reset --hard HEAD^
# Switch to the new branch to see your changes
git checkout right-branch
# Apply the commit to the right branch
git cherry-pick <commit-hash>
</pre>

Till next time.
