---
layout: post
title: "How to integrate source code manged by Perforce into a Git project?"
description: ""
category:
tags: [git, perforce, code, vcs]
---
{% include JB/setup %}

#### Motivation

The company I work for traditionally uses [Perforce](http://www.perforce.com/) as version control system. When I
joined the team we started a new project without any dependencies to the existing Perforce code base. This allowed us
to use [Git](http://git-scm.com/) instead. A couple of months later my team got quite proficient
with the Git workflow and we moved more and more projects to Git. Even other teams got excited about our
workflow and started using Git themselves. As a consequence to the increased Git usage we faced the following
problem: How to integrate
existing source code managed by Perforce into new Git projects?


#### Approach

The first approach was a product form Perforce called [Git Fusion](http://www.perforce.com/product/components/git-fusion).
This product allows to interact with Perforce as it would be a Git repository. Unfortunately Git Fusion did not work
for us. First, there is no support for Perforce streams [yet](http://stackoverflow.com/a/16463532/353652).
Second, there is no out-of-the-box integration into common Git web interfaces such as [GitHub Enterprise](https://enterprise.github.com/).

The second approach was a tool from Git called [git-p4](http://git-scm.com/docs/git-p4) ([here](http://owenou.com/2011/03/23/git-up-perforce-with-git-p4.html) is a good tutorial how to setup git-p4). Based on that tool I build
a service that pushes Perforce changes to Git. One major difference to the Git Fusion approach is that my service is
a one way bridge. That means source of record for an imported project will remain Perforce. This is kind of an artificial
restriction but it makes the service a lot simpler (Why? Imagine two developers create a conflicting change at the
same time on the same branch. How do you resolve that automatically?).

The expected workflow is depicted below. The service polls Perforce for new changes __(1)__ and pushes them
to a Git repository __(2)__. This Git repository can integrated as a [Git Submodule](http://git-scm.com/book/en/Git-Tools-Submodules)
into other Git repositories __(3)__ and cloned by the developers __(4)__.

![git-p4 bridge overview]({{ site.url }}/assets/images/gitp4-bridge.png)


#### Problems

Usually you cannot clone an entire Perforce repository to Git due to the different usage patterns of these tools.
In Perforce you typically have one repository for all your projects including third party dependencies and assets (at
least that is how the company I work for does it). In Git you typically have one repository for each project and you
try to _outsource_ third party dependencies and assets as much as possible to maintain a responsive repository (at least
that is how I do it).

As a consequence you need to carefully define what part of the Perforce repository ends up in your Git repository.
For that purpose the `git-p4 clone` operation allows to define an ignore pattern. However, this ignore pattern is only
applied on the initial sync. Any subsequent sync operation via `git-p4 rebase` will not respect this ignore pattern.

Another caveat for big and slow Git repositories is the "@all" option passed to `git-p4 clone`. You usually want to
set this option to import the entire history. In order to keep you Git repository small I recommend to ignore directories
that used to be big and have already been deleted. But how do you find huge directories and files?
After your initial import you run [this](http://stubbisms.wordpress.com/2009/07/10/git-script-to-show-largest-pack-objects-and-trim-your-waist-line/) script on your Git repository. If you find anything big that is not useful then add it to the ignore pattern
and import the repository again.


#### Solution

I created the [git-p4-bridge](https://github.com/larsxschneider/git-p4-bridge) project to solve the problems mentioned
above. Here is how you use it:

1. Define the source path of your Perforce repository that you want to sync to Git.
2. If necessary, create a list of ignored directories and store them in a file (see [example](https://raw.githubusercontent.com/larsxschneider/git-p4-bridge/master/example.ignore)). This file is used to ignore all irrelevant Perforce paths on
the [initial sync](https://github.com/larsxschneider/git-p4-bridge/blob/master/pull-p4-push-git.sh#L62) as well
as on every [subsequent sync](https://github.com/larsxschneider/git-p4-bridge/blob/master/pull-p4-push-git.sh#L82). 
3. Run the [pull-p4-push-git.sh](https://github.com/larsxschneider/git-p4-bridge/blob/master/pull-p4-push-git.sh) script:

        ./pull-p4-push-git.sh \
            //P4/Project@all tcp:P4Server:1672 P4User P4Pass \
            git@GitServer:Project.git GitBranch /path/to/git-ssh.key \
            /path/to/ignore.pattern

If this works as expected you can [add](https://github.com/larsxschneider/git-p4-bridge/blob/master/daemon-bridge.sh#L16)
the call to a script similar to [daemon-bridge.sh](https://github.com/larsxschneider/git-p4-bridge/blob/master/daemon-bridge.sh)
in order to run it periodically. I usually run this script in a [tmux](http://robots.thoughtbot.com/a-tmux-crash-course)
session on a server.


#### Outlook

If you see a simpler way to achieve the solution above I am happy to hear from you! I also have a script to sync into
the opposite directory. In that setting the Git repository is the source of record and the code is pushed to a Perforce
directory. This is a possible topic for a future post.


#### Acknowledgements
Thanks to [Ronny](https://github.com/esterlus) and Norman for proofreading! 

