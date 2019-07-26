---
title: git 分支介绍
date: 2019-06-12 22:51:52
tags: git
---

## local branch
You can view a list of all the local branches on your machine by running git branch:
```
$ git branch
master
new-feature
```
Each local branch has a file under .git/refs/heads/:
```
$ ls -F .git/refs/heads/
master new-feature
```
There are two types of local branches on your machine: non-tracking local branches, and tracking local branches.
### Non-tracking local branches
Non-tracking local branches are not associated with any other branch. You create one by running `git branch <branchname>`.
### Tracking local branches
Tracking local branches are associated with another branch, usually a remote-tracking branch. You create one by running `git branch - track <branchname> [<start-point>]`.
You can view which one of your local branches are tracking branches using `git branch -vv`:
```
$ git branch -vv
master b31f87c85 [origin/master] Example commit message
new-feature b760e04ed Another example commit message
```
From this command's output, you can see that the local branch master is tracking the `remote-tracking` branch `origin/master`, and the local branch new-feature is not tracking anything.
Another way to see which branches are tracking branches is by having a look at `.git/config`.
Tracking local branches are useful. They allow you to run git pull and git push, without specifying which upstream branch to use. If the branch is not set up to track another branch, you'll get an error like this one:
```
$ git checkout new-feature
$ git pull
There is no tracking information for the current branch.
Please specify which branch you want to merge with.
See git-pull(1) for details
git pull <remote> <branch>
If you wish to set tracking information for this branch you can do so with:
git branch - set-upstream new-feature <remote>/<branch>
```
## Remote-tracking branches (still on your machine)
You can view a list of all the remote-tracking branches on your machine by running `git branch -r`:
```
$ git branch -r
bitbucket/master
origin/master
origin/new-branch
```
Each remote-tracking branch has a file under `.git/refs/<remote>/`:
```
$ tree -F .git/refs/remotes/
.git/refs/remotes/
├── bitbucket/
│ └── master
└── origin/
├── master
└── new-branch
```
Think of your remote-tracking branches as your local cache for what the remote machines contain. You can update your remote-tracking branches using `git fetch`, which `git pull` uses behind the scenes.
Even though all the data for a remote-tracking branch is stored locally on your machine (like a cache), it's still never called a local branch. (At least, I wouldn't call it that!) It's just called a remote-tracking branch.
## Branches on a remote machine:
You can view all the remote branches (that is, the branches on the remote machine), by running `git remote show <remote>`:
```
$ git remote show origin
* remote origin
Fetch URL: git@github.com:Flimm/example.git
Push URL: git@github.com:Flimm/example.git
HEAD branch: master
Remote branches:
io-socket-ip new (next fetch will store in remotes/origin)
master tracked
new-branch tracked
Local ref configured for 'git pull':
master merges with remote master
new-branch merges with remote new-branch
Local ref configured for 'git push':
master pushes to master (up to date)
new-branch pushes to new-branch (fast-forwardable)
```
This git remote command queries the remote machine over the network about its branches. It does not update the remote-tracking branches on your local machine, use `git fetch` or `git pull` for that.
## example
git pull [<options>] [<repository> [<refspec>…​]]


git merge [-n] [ - stat] [ - no-commit] [ - squash] [ - [no-]edit]
 [-s <strategy>] [-X <strategy-option>] [-S[<keyid>]]
 [ - [no-]allow-unrelated-histories]
 [ - [no-]rerere-autoupdate] [-m <msg>] [-F <file>] [<commit>…​]
从官方的文档来看，git merge是没有repository参数的
```
$ git pull origin next
```
这个pull的直接就是远程的分支, 他是下面两个命令的结合体
```
$ git fetch origin
$ git merge origin/next
```
这个先把远程的所有分支fetch过来，然后把远程跟踪分支merge到当前分支，origin/next这种分支是存在本地的,虽然叫远程跟踪分支
把远程的分支fetch过来可以看到如下， fetch就是把远程的东西拿到本地的仓库，也就是origin/develop
```
C:\git\testgit>git fetch
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://gitprod.statestr.com/e587214/testgit
8ce7bef..0605953 develop -> origin/develop
```
## ref
https://stackoverflow.com/questions/16408300/what-are-the-differences-between-local-branch-local-tracking-branch-remote-bra/24785777