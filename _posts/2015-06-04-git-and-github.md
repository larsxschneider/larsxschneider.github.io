---
layout: post
title: "Git and GitHub - An Introduction"
description: ""
category:
tags: [git, github, scm]
---
{% include JB/setup %}

Keeping source code under version control is the way we software engineers stay sane and still deliver high quality software, while working simultaneously in the same code base. In recent years, Git has emerged as a popular choice over SVN, CVS, and Perforce for several reasons. In this article, I will give you a brief introduction to Git and GitHub.

#### What is so special about Git?
[Git](http://www.git-scm.com/) is a *distributed version control system*. That means that there is no central server that manages your source code. Instead, every Git user's computer manages the entire source code. Consequently, you can edit your code and create revisions independent of your network conditions. You can work wherever you’re most productive - at the coffee shop, on the roof deck, or on an airplane - even if your network is down. Since Git operations are executed locally, Git is lightning fast. You can switch to an older revision to test a bug in the blink of an eye.

However, this locality comes with a catch. Whatever you add to Git will stay in Git (unless you perform a Git rebase operation which has its own idiosyncrasies). Git was primarily designed for plain text files, e.g. source code, and can handle these files efficiently. The Linux kernel project with more than [15 million lines of code](http://arstechnica.com/business/2012/04/linux-kernel-in-2011-15-million-total-lines-of-code-and-microsoft-is-a-top-contributor/) is managed using Git and is an impressive example of its performance. Nevertheless, huge binary files that are frequently changed will make Git slow and you should find [alternative solutions](https://git-lfs.github.com/) for them.

Because Git users work locally, everyone works on an independent development branch with its own history. All these development branches are merged over time into one master branch that in turn serves as a base for new development branches. Branching and merging are essential to Git and are executed quickly.

Last but not least, Git is very hard to compromise. Every revision in Git has an unique identifier (the [SHA-1 hash](https://en.wikipedia.org/wiki/SHA-1)) that depends on the content of the source code of the entire history up to and including the current revision. If an attacker changes only one character in one file in the history, then all subsequent hashes would change and Git would detect the fraud.

#### What are the important Git terms?
Git stores all the files of one project in a **repository**. A repository has one or more **branches**. A branch (or stream, in Perforce terms) has a descriptive name and represents an independent line of development. Each branch consists of one or more **commits**. A commit (or changelist, in Perforce terms) contains a list of changes, a reference to the previous commit, an author, a date and a **commit message** which explains what the change is about. A **merge commit** is a special kind of commit that merges two branches into one by referencing two previous commits. Every commit is uniquely identified by its commit **hash** (changelist number in Perforce terms).

If you want to work on an existing repository that is stored remotely, you need to **clone** it to your local machine first. If you want to switch to a particular branch, you need to **checkout** this branch. The **HEAD commit** represents the latest commit on the currently checked out branch. A local branch can have a remote companion that is called the **remote tracking branch**. You **pull** the latest remote changes from the remote tracking branch, and you **push** your local changes to the remote tracking branch.

The **working copy** is your scratch pad on top of the currently checked out branch. Git will automatically detect any changes you make and present them as changes that are **not staged**. If you want to create a new **commit** then you move some or all changes to the staging area. A commit is created based on whatever is in the staging area.

This is just a very brief overview to get you started. You can learn more about Git [online](https://try.github.io/levels/1/challenges/1) or with the [Pro Git book](https://git-scm.herokuapp.com/book/en/v2) which I highly recommend.

#### What is GitHub?
[GitHub](https://github.com) is a web front-end for Git remotes. It allows you to see the source code of the master branch as well as all uploaded (pushed) development branches (see [here](https://github.com/larsxschneider/ShowInGitHub) for an example).

In general, not every developer has write access to every repository. To make changes regardless, you can [fork a repository](https://guides.github.com/activities/forking/). A fork creates a full copy of a repository in your own GitHub account and grants you write access to this copy. You can [clone](https://guides.github.com/activities/forking/#clone) this copy to your local machine and [push changes](https://guides.github.com/activities/forking/#making-changes) to it. If you want to move your change to the official, also called upstream repository, you need to [create a pull request](https://guides.github.com/activities/forking/#making-a-pull-request). The owner of the upstream repository can review your changes and merge them with the click of a button.
