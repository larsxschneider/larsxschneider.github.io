---
layout: post
title: "GitHub - Tips and Tricks"
description: ""
category:
tags: [github]
---
{% include JB/setup %}

An increasing number of projects move their source code to Git using [GitHub](http://github.com). Frictionless code sharing/collaboration and state-of-the-art development processes such as [feature based branches](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow) are just a few examples of the many advantages that teams are leveraging after the switch. This post kicks off a series of posts to share GitHub tips and tricks that will boost your productivity even further.

#### Source Code Links
As software engineers, we have discussions with our colleagues about source code on a daily basis. This can be challenging if the team is distributed, as you can’t just point out a line on the screen to a remote coworker. Using GitHub you can highlight relevant lines online and send them via instant messenger or email to your peers. 

*Here is how you do it:*

First, you open the repository of your project with your browser on GitHub (e.g. the project [ShowInGitHub](https://github.com/larsxschneider/ShowInGitHub)). You can either use the GitHub file browser to navigate to your desired file or you press **'t'** and start typing the filename to **find the file**.

After opening a file, you can **click** on any **line number** on the left to **highlight** a line. Copy/paste the browser URL and send it to your colleagues. They will see the same file with the same highlighted line as you do if they open the link ([example](https://github.com/larsxschneider/ShowInGitHub/blob/master/Source/Classes/SIGPlugin.m#L80)). But that's not all! You can even highlight entire code **sections** if you press **SHIFT and click** on the closing line number ([example](https://github.com/larsxschneider/ShowInGitHub/blob/master/Source/Classes/SIGPlugin.m#L80-L104)).

One quirk to watch out for is that GitHub always highlights the same line number on the latest version of a file by default. Let's say you highlight line 10 and you send the resulting link via email. If someone inserts 3 lines at the beginning of the file then your link will not highlight the correct line anymore. The link will still highlight line 10 but it should highlight line 13. To address this problem and make the line numbers stable, I recommend to always **pinpoint** your GitHub links to a specific point in time, that means to a specific Git commit. You can do this by pressing **'y'** right after highlighting ([example](https://github.com/larsxschneider/ShowInGitHub/blob/ecf9d1cb40c011d3ba1a51ca785a1cfcb51e8c96/Source/Classes/SIGPlugin.m#L80-L104)).

A number of editors support GitHub link creation directly from the source code via plugins ([Xcode](https://github.com/larsxschneider/ShowInGitHub), [Sublime](https://github.com/ehamiter/ST2-GitHubinator), [Atom](https://github.com/atom/open-on-github), [Vim](https://github.com/solars/github-vim), ...). This speeds up the process as you don't have to find the file and line number via your browser.

This is all there is to know about GitHub source code link creation. I hope you enjoyed the article. In the next issue I will cover how to get the most out of your source code history using GitHub. 
