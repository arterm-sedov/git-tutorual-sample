---
description: >-
  This tutorial was commissioned as a test task: create a Git introduction based
  on a YouTube video
---

# Git Basics. Working with Branches

## Introduction

## Prerequisites

This tutorial assumes knowledge of the following foundational topics:

* Git commit
* Working tree
* Staging area
* Creating a Git repo

These and several other essential topics are covered in the following video: [Introduction to Git — Core Concepts](https://www.youtube.com/watch?v=uR6G2v\_WsRA\&t=0s)

For more details on using Git, you can also refer to the _Pro Git_ book by Scott Chacon: [https://git-scm.com/book/en/v2](https://git-scm.com/book/en/v2)

This tutorial also relies on Vim text editor commands, while you can use your preferred editor.

## Setting up a Git Repo

To complete this tutorial you will need to create a Git repo and add some text files to work with version control.

For the tutorial, we will create a repo with a fake network automation project consisting of one folder: `netauto`, and two files: `S1` and `S2`.

1. Start a new git repo, using the following commands in your terminal:

```bash
mkdir netauto
cd netauto
git init
```

![Creating a repository](<Pasted image 20230128180400.png>)

2. Create a file named `S1`:

```bash
vim S1
```

3. Enter the following content in the `S1` file and save it:

```yaml
mgmt_ip: 10.0.0.1
vlans:
  red:
     id: 10
  blue:
     id: 20
ports:
 1: [ 20 ]
 2: [ 20 ]
 3: [ 10, 20 ] 
```

4. Stage and commit the `S1` file:

```bash
git add S1
git commit -m "create S1"
```

![Staging and committing the S1 file](<Pasted image 20230128185508.png>)

5. Duplicate the `S1` file to the `S2` file.
6. Stage and commit the `S2` file:

```bash
cp S1 S2
git add S2
git commit -m "create S2"
```

![Creating, staging, and committing the S2 file](<Pasted image 20230128190001.png>)

7. Check your two commits:

```bash
git log
```

![Viewing the Git log](<Pasted image 20230128190453.png>)

8. Check Git status:

```bash
git status
```

![Checking Git status](<Pasted image 20230128191154.png>)

You should see that Git created and named the `master` branch automatically in your new repo.

## Branch Definition

Branches separate different versions of the same files and allow you to work on them in parallel.

Edits on one branch are independent of work on other branches. This allows you to have branches for different purposes: a production branch, a development branch, and a bug-fix branch.

You can incorporate or merge changes from a branch into other branches.

To understand how a branch is implemented let's see its visual representation in the commit graph.

The diagram below shows the newly created repo with two commits. See [Setting up a Git Repo](./#setting-up-a-git-repo).

![A commit graph diagram with two commits on the master branch](<Pasted image 20230130142315.png>)

A Git branch is essentially a pointer to a 40-digit hexadecimal SHA-1 hash of a commit. In this tutorial, we will refer to the first 7 characters of these hashes.

When you are on a certain branch, every time you make a commit, the branch moves up to your latest commit.

## HEAD Definition

Git knows which branch you are using a special pointer called HEAD.

HEAD normally points to a branch and not directly to a commit. Sometimes HEAD is called a symbolic pointer.

So far, you only have the first branch: `master`, and HEAD points to it.

In Git terminology, the HEAD pointer tells you what you have checked out.

So from the [diagram above](./#branch-definition) you can see that the master branch is checked out.

To see the labeled commit graph in the terminal use some additional options with the `git log` command:

```bash
git log --all --decorate --oneline --graph
```

![A decorated commit graph](<Pasted image 20230130144445.png>)

This command shows that you are on the second commit due to the HEAD pointer. The HEAD pointer is pointing to the `master` branch. This tells that you have the `master` branch checked out.

{% hint style="success" %}
**Tip**

You can save the `git log --all --decorate --oneline --graph` command as an alias called `graph` for future use:

<pre class="language-bash"><code class="lang-bash"><strong>alias graph="git log --all --decorate --oneline --graph"
</strong></code></pre>
{% endhint %}

## Creating Branches

To create new branches use the `git branch <branch_name>` command.

The branches will be instantiated where the HEAD pointer is.

1. Start two new branches: `SDN` and `auth`.
2. View all branches using the `git branch` command.

```bash
git branch SDN
git branch auth
git branch
```

![Creating and viewing the branches](<Pasted image 20230130171443.png>)

You now have three branches: `master`, `auth`, `SDN`.

There is an asterisk next to the master branch, and it is green. This indicates that you have the `master` branch checked out. More precisely, the HEAD pointer points to the master branch.

3. View the decorated commit graph:

```bash
graph
```

![Commit graph with three branches pointing to one commit](<Pasted image 20230130172147.png>)

You can see all three branches pointing to the same commit: `create S2`.

The HEAD is attached to the `master` branch since you have the `master` branch checked out.

## Checking out and Working on Branches

You have three branches: `master`, `SDN`, and `auth`.

![Commit diagram with three branches on the same commit](<Pasted image 20230130174926.png>)

1. Checkout the `SDN` branch and view the commit graph and Git status:

```bash
git checkout SDN
graph
git status
```

![Git HEAD pointing to SDN branch](<Pasted image 20230131161504.png>)

When you checked out the `SDN` branch, the HEAD pointer moved to point to the `SDN` branch. Now the `graph` alias command shows HEAD has moved and points to `SDN`. The `git status` command shows the same.

3. While you're are on the `SDN` branch, edit the `S1` file by adding an SDN controller IP (for example, using the `vi S1` command):

```yaml
mgmt_ip: 10.0.0.1
sdn_controller: 10.20.2.2
vlans:
  red:
     id: 10
  blue:
     id: 20
ports:
 1: [ 20 ]
 2: [ 20 ]
 3: [ 10, 20 ] 
```

4. Stage and commit that change:

```bash
git add S1
git commit -m "SDN for S1"
graph
```

![SDN branch moved to the new "SDN for S1" commit](<Pasted image 20230131172430.png>)

Since you have the `SDN` branch checked out, only the `SDN` branch moved up to the new commit. The master and auth branches stay where they are: at the previous commit.

5. See the newest change, the SDN controller IP is there:

```bash
cat S1
```

![The S1 file with an added line](<Pasted image 20230131172640.png>)

6. Now let's work on the `auth` branch and see the resulting commit graph:

```bash
git checkout auth
git branch
git graph
```

![Checking out the auth branch](<Pasted image 20230131173806.png>)

The `checkout` command moved the HEAD pointer from `SDN` to `auth`.

7. See that the newly added `sdn_controller` line is not present anymore in the `S1` file using the `cat S1` command.

![A version of the S1 file on the auth branch](<Pasted image 20230131174030.png>)

Git replaced your working tree and staging area to match the commit the `auth` branch is associated with. At this earlier commit, you don't have the SDN controller change.

10. While on the `auth` branch, make a different change to the `S1` file to map the S1 switch to an authentication server:

```yaml
mgmt_ip: 10.0.0.1
vlans:
  red:
     id: 10
  blue:
     id: 20
ports:
 1: [ 20 ]
 2: [ 20 ]
 3: [ 10, 20 ]
auth_server: 10.30.3.3
```

12. See that you've modified the `S1` file in the working tree:

```bash
git status
```

![Git status with unstaged changes](<Pasted image 20230131174941.png>)

13. Stage and commit that change using a shortcut option `-a` with the `git commit` command, that stages all tracked files, that have been modified:

```bash
git commit -a -m "auth for S1"
```

12. View the resulting commit graph:

```bash
graph
```

![A new commit in the auth branch and its graph](<Pasted image 20230131182710.png>)

You have created a new commit, and only the `auth` branch moved to it.

You have started at the `SDN for S1` commit. Master is still pointing to this commit.

From this base, you branched out in two different directions:

* You have checked out the `SDN` branch and added an SDN controller IP.
* Then you checked out the `auth` branch and added an authentication server IP.

You have ended up with 3 branches: `auth`, `SDN`, and `master`, pointing to different commits.

The content of the `S1` file will be different depending on which branch you checkout.

![Resulting commit graph diagram with three branches](<Pasted image 20230131182322.png>)

## Merging branches

Now let's say your work is done on the `SDN` and `auth` branches, and you want to integrate these changes back into the `master` branch: to merge your new changes into the `master` branch.

We will talk about two types of merges:

* [fast-forward merge](./#fast-forward-merge)
* [3-way merge](./#3-way-merge)

### Fast-Forward Merge

Let's get the SDN controller config into your `master` branch.

To merge SDN into master, use the `git merge` command.

The commit where you added the SDN controller is `6889e71`. The `SDN` branch is pointing to it.

This commit's parent is `4fe9c36`. This is where the `master` branch is.

![The commits leading to the SDN branch with added SDN controller line](<Pasted image 20230131191413.png>)

Since there is a direct path from `master` to `SDN`, Git can perform a so-called fast-forward merge.

During the fast-forward merge Git just moves the `master` branch to where `SDN` is: the `master` just has to catch up with the `SDN`.

Even if there were multiple commits between the two branches, there is still a fast-forward merge: you just need a direct path.

1. Check the status to see that you're still on the `auth` branch.
2. Check out the `master` branch.
3. See, shows what will change when you merge `SDN` into `master`:

```bash
git status
git checkout master
git diff master..SDN
```

![Viewing the difference between the master and SDN branches](<Pasted image 20230131190858.png>)

The `git diff` command shows that the `S1` file in the `master` branch will get the `sdn_controller` line from the `SDN` branch.

4. From the master branch merge SDN, see the resulting `S1` file and graph:

```bash
git merge SDN
cat S1
graph
```

![The merged master and SDN branch commit graph with the resulting S1 file](<Pasted image 20230131191935.png>)

Git confirms you have done a fast-forward merge.

Git has added one line to S1, and `cat S1` shows this.

With the `graph` alias command, you see that the `master` branch is caught up with the `SDN` branch: Git has moved the pointer to the same commit where `SDN` is.

5. As you don't need two branches pointing to the same commit, you can delete the `SDN` branch. See [Deleting Branches](./#deleting-branches).

### 3-Way Merge

Now that you've merged and deleted the `SDN` branch let's do the same for the `auth` branch.

Looking at the commit graph, you can see there is no direct path from the `master` branch to the `auth` branch. Git can not do a [fast-forward merge](./#fast-forward-merge) this time. For this case, a 3-way merge will happen.

![The branches that require a 3-way merge](<Pasted image 20230131194216.png>)

To merge `master` and `auth`, you can't just move the `master` pointer to the `ff29a74` commit. If you did that, you would lose the `SDN` changes made in the `6889e71` commit.

You need to merge these branches into a new commit called a merge commit. To make this merge commit, Git looks at three commits:

* First, the base commit `4fe9c36` the two branches started from.
* Then the last commit of each branch: `ff29a74` and `6889e71`.

![3-way merging commit graph diagram with the resulting merge commit](<Pasted image 20230131195721.png>)

1. Check Git status, merge the `auth` branch, and see the resulting graph:

```bash
git status
git merge auth
graph
```

![The result of merging the auth branch](<Pasted image 20230131195530.png>)

The output does not say `fast-forward` merge [like when merging the `SDN` branch](./#fast-forward-merge). Now it says “Merge made by the 'ort' strategy.”

The `graph` alias command shows the merge commit `bf80100` joining the two branches.

2. Now check that you can safely [delete](./#deleting-branches) the auth branch and delete it:

```bash
git branch --merged
git branch -d auth
```

![Deleting the unneeded auth branch](<Pasted image 20230131200751.png>)

## Deleting Branches

You can safely delete merged branches keeping all changes to their files.

You can also force-delete unmerged branches losing all their changes.

1. Before deleting the branch see what commits are merged into it:

```bash
git branch --merged
```

2. The SDN branch was [merged earlier](./#fast-forward-merge), so you can delete it:

```bash
git branch -d SDN
```

![Deleting a merged branch](<Pasted image 20230131193430.png>)

3. If you try to delete a branch that was not yet merged, Git will not allow this:

![Git blocking unmerged branch deletion](<Pasted image 20230131193532.png>)

4. To delete the branch that was not merged and lose all its changes use the upper case `-D` option:

```bash
git branch -D SDN
```

## Git Command Reference

* `git log` — view git history.
* `git log --all --decorate --oneline --graph` — view commit history graph.
* `git branch <branch-name>` — create a branch.
* `git checkout <branch-name>` — checkout a branch/move head pointer
* `git commit -a -m "commit message"` — commit all modified and tracked files in one command (bypass separate `git add` command).
* `git diff master..SDN` — diff between 2 branches.
* `git merge <branch-name>` — merge branches (fast-forward and 3-way merges).
* `git branch --merged` — see branches merged into the current branch.
