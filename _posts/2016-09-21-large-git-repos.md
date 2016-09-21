---
layout: post
title: "Large Git Repositories"
description: ""
category:
tags: [git,git-lfs]
---
{% include JB/setup %}

Git repositories can be considered large in a number of different dimensions.
Exceeding certain thresholds for these dimensions can impact the performance
of your local Git operations dramatically. Depending on your hardware,
operating system, and Git version these thresholds are very different. Git
generally performs better on a SSD drive compared to a spin disk. On Linux, Git
commands usually run faster compared to Windows (OSX is somewhere in the
middle). The latest Git version is generally better than any previous version
on all platforms. In the following sections I will discuss various ways a Git
repository can grow large. The focus will be on repositories that contain
large files.

### Large by File Size

A large Git repository can be a repository that contains large files. Large
files are no problem for Git in general. It is rather that Git is designed and
optimized for source code text files, and larger files make it cumbersome to
use. Due to the distributed nature of Git, a repository generally contains all
history. That means, even if you have deleted that single 1 GB file in your
repository two years ago, it will be transferred on every clone since then.
Moreover, Git's compression algorithms are optimized for source code. If you
have changed that single 1 GB file seven times in the last two years, then a
single clone operation would transfer at least 7 GB. This is a problem.

#### What is "a large file" in the context of Git?

Size is actually not the most relevant factor. Compressibility and change
frequency are important. A number of larger XML files will usually not
increase the size of your repository as they usually compress well. A single,
already highly compressed, MP4 video file will most certainly increase your
repository size. A set of 100 image files with a size of 0.3 MB each would
usually have no significant impact on your repository size. However, if your 
hard-working designer changes them every day, then your repository could grow 
in a single year to the uncomfortable size of 10 GB (0.3 MB x 100 files x 365 days). Larger files and even smallish files that change often will always
increase your repository size.

In general, you should avoid adding these files to your Git repositories.
Especially if there are better solutions. A common source of problematic files
are compiled third party components that already have an own version
identifier. It is much more efficient to track only their version identifier
in Git and install the component with a package management system (e.g. via
 [Artifactory](https://www.jfrog.com/artifactory/)). Other large files, such as
integration test data, image/audio/video assets, or help files have legitimate
reasons to be stored and versioned along with the source code. A practical way
to handle these kind of files is to use a Git extension called [Git LFS](https
://git-lfs.github.com/).

_Rule of thumb: Repositories with files smaller than 500kb are fine._


#### What is Git LFS?

The content of a file managed by Git LFS is not stored in the Git repository.
Instead, the content is stored on a central server and downloaded only if
needed. The only data stored in the Git repository is the exact location of
the content. This mechanism solves the initially stated problem nicely. A 1 GB
file managed by Git LFS will not noticeably increase your repository size and
that continues to be true even if you change the file multiple times. This is
great, but the solution has a drawback that you should know about. A Git
repository that contains files managed by Git LFS is not distributed anymore
and it relies on a reachable server to function properly. If this arrangement
is acceptable for your use case, then Git LFS is a viable solution.

Git LFS identifies files that it should take care of only via file paths. The
most common way is to define a [glob
pattern](https://en.wikipedia.org/wiki/Glob_(programming)) for a specific file
type using the `git lfs track` command. For instance, `git lfs track "*.mp4"`
will leverage Git LFS for all `mp4` files in the repository. Please note that
only `mp4` files added after the invocation of `git lfs track` are managed by
Git LFS. This approach works reasonable well.

However, sometimes a certain file type matches a lot of really small files and
only few very big files (e.g. `png` files). Keeping all files in Git is no
option as the big files would increase the overall repository size. Moving all
files to Git LFS is no good option either because the LFS processing overhead
for all the small files make Git operations very slow. As of Git LFS version
1.4.x you should avoid too many files using Git LFS. In a future version this
limitation hopefully goes away as I am working on a [new, much more efficient,
protocol for Git LFS](https://git.github.io/rev_news/2016/08/17/edition-18/).

_Rule of thumb: Repositories with less than a 1000 files using Git LFS are
fine._


#### How to handle too many files in Git LFS?

Since the new protocol is not yet available, you need to work around the
limitation. You could define a directory in your repository for larger files
and track all files inside it using Git LFS. For instance, `git lfs track
"/big/*"` would track all files under `/big`.
Alternatively, you could add a certain string to the name of all files that you
want to track. For instance `git lfs track "*.lfs.*"` would track
`image1.lfs.png` but not `image2.png`.

Whatever scheme you use, it is very important to ensure that you do not commit
larger files to Git instead of Git LFS accidentally. As mentioned above, a
file committed to Git will stay in Git even if you move it to Git LFS
afterwards. The only way to correct this kind of mistake is to purge the file
from the history with a [Git rebase](https://git-scm.com/docs/git-rebase)
operation. However, I do not recommend to change the history carelessly in
larger teams as it creates a number of problems. A good way to avoid these
accidental commits are pre-commit hooks that check the size of new files
and code reviews.

If you indeed have a large number of legitimate Git LFS files, then your
repository performance can quickly degrade. You could use a ["sparse" checkout](http://vmiklos.hu/blog/sparse-checkout-example-in-git-1-7)
to exclude seldom used directories containing Git LFS files and speed up your
Git operations again.


### Large by File Count

A large Git repository can be a repository that contains a large number of
files in its head commit. This can negatively affect the performance of
virtually all local Git operations. A common mistake that leads to this
problem is to add the source code of external libraries to a Git repository.
Besides the performance degradation, you would lose the history of these
libraries which would make bug fixing and updating the library more
complicated in the long run. However, if your repository has a large number
for files for legitimate reasons (e.g. lots of test files) then a ["sparse" checkout](http://vmiklos.hu/blog/sparse-checkout-example-in-git-1-7), which only checks out certain directories, can be a viable option to
address performance problems.

_Rule of thumb: Repositories with less than a 100k files are fine._


### Large by Commit Count

A large Git repository can be repository with a deep history and a large
number of commits. Cloning this kind of repository and/or running certain
local Git commands on it (e.g. `git blame`) can be slow. In some cases, a
"shallow" clone, which only clones the most recent commits (`git clone
--depth <depth> <url>`), can be a viable solution. However, shallow clones
can require more processing on the server side and therefore a regular clone
might still be faster after all. Plus, local Git operations that require the
entire history (e.g. `git blame`) might not work as expected anymore.

_Rule of thumb: Repositories with less than a 100k commits are fine._


### Large by Branch/Tag Count

A large Git repository can be a repository that contains a large number of
branches and tags. Similar to a deep history, cloning such a repository can
take a lot of time and local Git operations (e.g. tab-complete a branch name)
can be slow.

_Rule of thumb: Repositories with less than a 10k branches and tags are fine._


### Large by Submodules Count

A large Git repository can be a repository that references a largish number of
Submodules. Git needs to query their status on all kinds of local operations
and this query operation is in particular slow on Windows.

_Rule of thumb: Repositories with less than a 25 Submodules are fine._

---

Special thanks to John Bigby for proofreading. Suggestion, comments, critique? Please drop me a note. Feedback is always appreciated!
