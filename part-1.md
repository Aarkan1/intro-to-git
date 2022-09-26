Teacher outlines Part 1

- [Version Control: What and why?](#version-control-what-and-why)
  - [What is version control?](#what-is-version-control)
  - [Why does history matter?](#why-does-history-matter)
  - [Multiple developers](#multiple-developers)
  - [Development Workflows](#development-workflows)
- [Getting Started](#getting-started)
  - [Local Operations](#local-operations)
  - [git init](#git-init)
  - [What just happened?](#what-just-happened)
- [Basic Operations](#basic-operations)
  - [Adding and Committing](#adding-and-committing)
  - [Adding new files](#adding-new-files)
  - [Committing changes](#committing-changes)
  - [Directories](#directories)
  - [git status](#git-status)
  - [.gitignore](#gitignore)
  - [git diff](#git-diff)
  - [git log](#git-log)
  - [git show](#git-show)
  - [Deleting files](#deleting-files)
  - [Moving files](#moving-files)
- [A Peek Inside Git](#a-peek-inside-git)
  - [SHA-1 Hashes](#sha-1-hashes)
  - [Writing SHA-1 Hashes](#writing-sha-1-hashes)
  - [Why not a revision number?](#why-not-a-revision-number)
  - [The DAG](#the-dag)
  - [And that's Git!](#and-thats-git)
- [Local Branching and Merging](#local-branching-and-merging)
  - [Creating and listing branches](#creating-and-listing-branches)
  - [Switching to a branch](#switching-to-a-branch)
  - [Committing on the branch](#committing-on-the-branch)
  - [So, what is a branch?](#so-what-is-a-branch)
  - [HEAD](#head)
  - [What a commit does](#what-a-commit-does)
  - [Branch switching is "in place"](#branch-switching-is-in-place)
  - [Merging](#merging)
  - [Fast-forward "merges"](#fast-forward-merges)
  - [Merge commits](#merge-commits)
  - [gitk](#gitk)
  - [Coping with conflicts](#coping-with-conflicts)

---

## Version Control: What and why?

### What is version control?

Version control systems are tools that help us to:

-   Keep track of the _history of changes_ to a set of files (source code, documentation, whatever)
-   Handle _multiple developers working in parallel_ on that set of files in a safe and efficient manner
-   Cope with _complex development workflows_, such as bug fix releases to existing versions of the software, isolated development and testing of new features and customer specific versions

### Why does history matter?

Means that it is possible to _answer questions_ about how the software got to its current state

Can find out _who is responsible_ for a particular change or piece of code, if you need to ask something about it

Helps with implementing a system of _code review_; a good commit should be a good unit of review

Part of implementing _continuous integration_
(e.g. can automatically test each commit)

### Multiple developers

As soon as more than one person is working on a system, there is a risk of conflicting changes

Two people changing the same file but in different places should not be a problem
_? want a version control system to handle the easy cases automatically, without fuss!_

Real conflicts should be flagged up, so they can be resolved correctly
_? want a version control system to tell us when we need to intervene and fix conflicts by hand_

### Development Workflows

A good VCS should support the way you develop

-   Develop and test new features in isolation
-   Must review code before it goes in mainline
-   Need to fix bugs in old releases
-   Need to take patches from outside of the core team
-   Changes go to testing, then deployment

## Getting Started

### Local Operations

For the first part of the course, we will be working with Git in an entirely local setting.

This means that you will be creating a repository and performing operations on your own machine.

Due to its distributed nature, Git allows you to do everything locally, from simple things like commits and history viewing through to more complex operations such as branching and merging.

Very different from centralized version control!

### git init

Creating a Git repository takes about 15 seconds!

1. Create a directory

    `$ mkdir my-first-git`

2. Change into that directory

    `$ cd my-first-git`

3. Tell Git to create a repository there

    `$ git init`

### What just happened?

The init command creates a .git directory.

This is where the entirely version history of the repository will be stored.

Also contains a config file, which we will not have to touch for now.

_The .git folder contains everything that matters. Want to give your full version history to a friend? Just zip up .git and send that; the "current version" can be recreated from what's in .git._

## Basic Operations

### Adding and Committing

In Git, committing is a two-step process
(but often, you can perform both with a single command)

1. Add things into the staging area
2. Commit what's in the staging area

### Adding new files

The `add` command places a file in the staging area

`$ git add README`

The `commit` command then takes the contents of the staging area, and creates a commit

`$ git commit -m "Add a README"`

```git
[master (root-commit) beb9dfd] Add a README.
 1 files changed, 1 insertions(+), 0 deletions(-)
 create mode 100644 README
```

### Committing changes

With existing files, _`add` places the changes to that file into the staging area_. This means that you can commit changes with the following two commands:

`$ git add README`

`$ git commit -m "Update README"`

Usually, however, this is done just with `commit`, specifying the file(s) to be committed:

`$ git commit -m "Update README" README`

### Directories

If you use `add` with a file inside a directory, it will retain the directory structure

`$ git add src/main.js`

`$ git commit -m "Start le coding!"`

_There is only one .git at the top level of the repository, not one inside every directory._

_**Minor limitation**: Git doesn't support putting empty directories under version control._

### git status

Gives an overview of what is currently staged, changed in tracked files and untracked.

```git
# On branch master
# Changes not staged for commit:
#
#       modified:   src/main.js  <-- These are changes to files Git knows about, but that are not staged
#
# Untracked files:
#
#       src/util.js         <-- These are files Git does not know about
#       src/styles.css
```

**TIP:** If you get fed up of typing status, alias it to st

`$ git config --global --add alias.st status`

Let's add one file and look at the status again...

`$ git add src/util.js`

`$ git status`

```git
# On branch master
# Changes to be committed:
#
#       new file:   src/util.js  <-- This file is now in the staging area
#
# Changes not staged for commit:
#
#       modified:   src/main.js  <-- Not in staging, but a commit of src would include it
#
# Untracked files:
#
#       src/styles.css
```

### .gitignore

One annoyance is that after building our code, the generated files show up in the status output.

```git
# On branch master
# Untracked files:
#
#       main.exe
#       main.obj
#       util.jar
```

The solution is to add a .gitignore file.

**.gitignore**

```
*.exe
*.obj
*.jar
```

`$ git add .gitignore`

`$ git commit -m "Add a .gitignore"`

### git diff

The `status` command gives you an overview of your changes; `diff` gives the details of what's `diff`erent.

```git
diff --git a/src/util.js b/src/util.js
index 9fe5927..e826a45 100644
--- a/src/util.js
+++ b/src/util.js
@@ -1,7 +1,7 @@
 #include <stdio.h>

-void print_ten_times(msg) {
+void print_n_times(msg, n) {
     int i;
-    for (i = 0; i < 10; i++)
+    for (i = 0; i < n; i++)
         console.log(msg);
 }
```

Red and the "-", indicate removed lines.

Green and the "+" indicate added lines.

By default `diff` only shows unstaged changes; used the `staged` option to see the staged changes.

`$ git add src/main.js`

`$ git diff --staged`

```git
diff --git a/src/main.js b/src/main.js
index 5f8a873..81f2948 100644
--- a/src/main.js
+++ b/src/main.js
@@ -1,5 +1,5 @@
 #include "util.h"

 function main() {
-    print_ten_times("Hello, world!\n");
+    print_n_times("Hello, world!\n", 10);
 }
```

The `stat` option will just give you statistics on the changes that have been made to each file.

`$ git diff --stat`

```git
src/main.js    |    2 +-
src/util.js    |    4 ++--
src/styles.css |    2 +-
3 files changed, 4 insertions(+), 4 deletions(-)
```

For many more options that can be used with diff, see:

`$ git help diff`

### git log

Shows a (automatically paged) log of all the commits, most recent commit first.

```git
commit e20962ebed7b0288922320f217a6a3ab9371727c
Author: johan <johan@edument.se>
Date:   Wed Apr 18 18:09:02 2012 +0200

    Add a .gitignore

commit eae16e7a7f34d1208ca8267c2fabbbc1eb8e3640
Author: johan <johan@edument.se>
Date:   Wed Apr 18 17:56:55 2012 +0200

    Factor printing out to a utility file

...
```

`log` has more options than you can possibly imagine; one fairly useful one is:

`$ git log --oneline`

```git
e20962e Add a .gitignore
eae16e7 Factor printing out to a utility file
887f06c Start le coding!
869cec3 Update README
8356287 Add a README
```

To learn about (literally) dozens more, see:

`$ git help log`

### git show

Shows you the full details of a commit

`$ git show 869cec3`

```git
commit 869cec3f4e200ded3e9b7d27823cfd4da8cd086d
Author: johan <johan@edument.se>
Date:   Wed Apr 18 17:34:10 2012 +0200

    Update README

diff --git a/README b/README
index 7062ca7..79a326b 100644
--- a/README
+++ b/README
@@ -1 +1 @@
-This project will be awesome!
+This project will be awesome! REALLY awesome!
```

### Deleting files

If you use the `rm` command, then the deletion of the file is immediately placed into the staging area.

`$ git rm README`

`$ git commit -m "Toss the README"`

Alternatively, you can just delete the file in your OS or IDE; a commit of the directory that contains it will then do the right thing.

`$ del README`

`$ git commit -m "Toss the README" .` <- dot is current directory

### Moving files

Once again, two options; the `mv` command which immediately stages the rename:

`$ git mv src\main.js src\hi.js`

`$ git commit -m "Rename the program"`

Or move it with your OS or IDE, and `git add` the new file; Git will figure out that it's the same file!

`$ move src\main.js src\hi.js`

`$ git add src\hi.js`

`$ git commit -m "Rename the program" src`

## A Peek Inside Git

### SHA-1 Hashes

Every commit is identified by a SHA-1 hash; we came across these when using log and show.

```git
commit e20962ebed7b0288922320f217a6a3ab9371727c  <-- Full hash of the commit
Author: johan <johan@edument.se>
Date:   Wed Apr 18 18:09:02 2012 +0200

    Add a .gitignore
```

The hash is _derived from the content of the commit_ along with the commit that came before it.

This means that the SHA-1 is not only unique locally, but _unique over all copies of a repository_!

### Writing SHA-1 Hashes

We have also seen SHA-1 hashes show up in an _abbreviated form_:

```git
e20962e Add a .gitignore
eae16e7 Factor printing out to a utility file
887f06c Start le coding!
869cec3 Update README
8356287 Add a README
```

Usually, the first six characters are enough to uniquely identify a commit in a repository.

You can provide as few or as many characters as you wish, provided they identify a single commit.

### Why not a revision number?

Using numbers would imply there is one unique ordering of commits.

This is fine if you have a centralized version control system, since the server can decide who gets to commit first if there is competition.

This doesn't make any sense in a distributed world, where different local copies will have commits that have not been shared yet.

_SHA-1 is always content-unique._

### The DAG

The version history is represented by a DAG (Directed Acyclic Graph) of commits.

Each commit points to the commit that came before it (or “commits” when there is a merge)

```
    *                   *
    |                  /|
    *                 * |
    |                 | |
    *                 * *
    |                 | |

simple,             with a merge
linear case         commit
```

### And that's Git!

Just about everything in Git boils down to...

a. Commits that are identified by a SHA-1

b. Manipulating the DAG of commits

The rest of this course will show you a whole array of different commands and patterns.

However, everything we will cover is really just about commits and placing them in graphs.

_The underlying model of Git is simple ☺_

## Local Branching and Merging

### Creating and listing branches

Creating a branch in Git is easy, and also an extremely cheap operation.

`$ git branch feature`

You can now get a list of branches that exist.

`$ git branch`

```git
  feature
* master
```

Notice the current branch is still master!

### Switching to a branch

The checkout command is used to switch from one branch to another.

`$ git checkout feature`

We can use the branch command again, to verify that we have indeed switched branch.

`$ git branch`

```git
* feature
  master
```

### Committing on the branch

We make a change, and check the status.

`$ git status`

```git
# On branch feature
# Changes not staged for commit:
#
#       modified:   src/util.js
#
```

It notes we are on the feature branch. We now go ahead and commit the usual way...

`$ git commit -m "Implement fib" src`

### So, what is a branch?

A branch is really just an alias for a commit.

Given every commit knows its predecessor, then knowing the latest commit in a branch is enough to know the full history of the branch.

```git
    * - feature
     \
      * - master
      |
      *
```

### HEAD

HEAD always refers to the current commit.

Usually, it does this by actually referring to the current branch, which in turn contains the SHA-1 of the latest commit on the branch.

```git
    * - feature, HEAD
     \
      * - master
      |
      *
```

### What a commit does

First, HEAD is used to find the current branch.
_(provided there is one; there is also a detached
HEAD mode, which we'll pass over for now)_

This in turn is used to get the SHA-1 of the current commit.

```git
abc123def
    * - feature, HEAD
     \
      * - master
      |
      *
```

A new commit is created, with its parent set to the current commit, which we just located.

It is SHA-1'd and stored by Git.

```git
    * abc123def
    |
    * - feature, HEAD
     \
      * - master
      |
      *
```

Finally, the branch alias is updated to point to the new commit, so it becomes the branch's latest.

Note that HEAD itself is not updated; it need not be as it points to the branch.

```git
    * - feature, HEAD
    |
    *
     \
      * - master
      |
      *
```

### Branch switching is "in place"

In some version control systems, different branches are stored in different directories, so moving between them is a directory change.

With Git, _using_ checkout to move _to a different branch changes the contents of the repository directory in place_, altering the files to represent the state they are in in the branch you switch to.

**Generally, this is a win; you can create a branch and start working in it without having to do a fresh build of your system! When switching, can just rebuild the changes.**

### Merging

Merging involves...

Doing a `checkout` of the target branch.

Using the `merge` command.

To merge fib-calc into master, we thus do:

`$ git checkout master`

`$ git merge feature`

```git
Updating 4d0df83..8e66580
Fast-forward
 src/util.js |    7 +++++++
 1 files changed, 7 insertions(+), 0 deletions(-)
```

### Fast-forward "merges"

Consider the DAG at this point in time:

```git
    * - feature, HEAD
     \
      * - master
      |
      *
```

_We have made no commits to master since we started the branch._

This means we can do a "fast-forward" merge.

In a fast-forward merge, we don't actually have to do any merging in the traditional sense.

Instead, we simply update the master branch's current commit to alias the latest commit in the feature branch - and we're done!

```git
    * - master, feature, HEAD
     \
      *
      |
      *
```

Fast-forward merges are cheap and completely painless - there's never going to be a conflict.

Doing development work in a local branch can be useful if you need to urgently task-switch back to master for doing a bug fix, or you discover a need to work on some other pre-requisite first.

**TIP:** If you're unsure whether you should do some work in a branch - **Just Do It**. If you turn out to need it, it's right there for you. If you don't need it, you get a painless, instant fast-forward merge.

### Merge commits

In many situations, there will have been _commits in both branches_; here we can see that master has a commit and feature-branch has a couple.

```git
    * - feature
    |
    * * - master
     \|
      *
      |
      *
```

A fast-forward is simply not possible here.

The output of a merge will look a little different:

```git
Auto-merging src/util.js
Merge made by recursive.
 src/hi.js      |    3 +++
 src/util.js    |    7 +++++++
 src/styles.css |    1 +
 3 files changed, 11 insertions(+), 0 deletions(-)
```

It doesn't mention fast-forward, but rather that there was a merge. (by the recursive algorithm)

Additionally, we are informed that both branches changed src/util.js, but Git handled that by itself.

This kind of merge also produces a merge commit, which has a couple of parents.

```git
              * - master
             /|
  feature - * |
            | |
            * *
             \|
              *
              |
              *
```

### gitk

As well as the command line tool, Git ships with a couple of small, but useful GUI applications.

One of them is `gitk`, which provides a nice way to explore the commit history.

It also gives a visual representation of the DAG.

### Coping with conflicts

There are some cases where a merge commit is needed, but Git can't completely work it out

For example, imagine feature-branch has this commit:

```git
 function main() {
     print_n_times("Hello, world!\n", 10);
+    console.log("Fib 5 = %d\n", fib(5));
+    console.log("Fib 10 = %d\n", fib(10));
 }
```

And master has this one:

```git
 function main() {
     print_n_times("Hello, world!\n", 10);
+    return 0;
 }
```

When we issue the merge command, we get some slightly scarier output:

```git
Auto-merging src/hi.c
CONFLICT (content): Merge conflict in src/hi.c
Auto-merging src/util.c
Automatic merge failed; fix conflicts and then commit the result.

```

This demands...manual intervention!

We open the conflicting file(s) in our editor; the conflicts are indicated as follows:

```js
function main() {
    print_n_times("Hello, world!\n", 10);
<<<<<<< HEAD
    return 0;
=======
    console.log("Fib 5 = %d\n", fib(5));
    console.log("Fib 10 = %d\n", fib(10));
>>>>>>> calc-fibs
}
```

The markers tell us which part came from which branch; we just edit the file to how it should be.

After editing, the file looks like this:

```js
function main() {
    print_n_times("Hello, world!\n", 10);
    console.log("Fib 5 = %d\n", fib(5));
    console.log("Fib 10 = %d\n", fib(10));
    return 0;
}
```

If we try and commit now, however, it won't work:

`$ git commit -m "Merge in feature"`

```git
U       src/hi.js
fatal: 'commit' is not possible because you have unmerged files.
```

We have to inform Git that we have resolved the conflict to our satisfaction; this is done using the `add` command:

`$ git add src/hi.js`

At this point, the `commit` command can be issued, and this time it will be successful.

`$ git commit -m "Merge in feature"`
