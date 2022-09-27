Teacher outlines

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
  - [git status](#git-status)
  - [Adding new files](#adding-new-files)
  - [Committing changes](#committing-changes)
  - [Committing with description](#committing-with-description)
  - [Directories](#directories)
  - [git diff](#git-diff)
  - [git log](#git-log)
  - [git show](#git-show)
  - [Deleting files](#deleting-files)
  - [Moving files](#moving-files)
  - [.gitignore](#gitignore)
- [More on staging](#more-on-staging)
  - [Unstaging](#unstaging)
  - [Stage just some changes in a file](#stage-just-some-changes-in-a-file)
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
- [Branch Workflows](#branch-workflows)
- [The Stash](#the-stash)
  - [What is the stash?](#what-is-the-stash)
  - [When to stash?](#when-to-stash)
  - [Stashing just unstaged stuff](#stashing-just-unstaged-stuff)
- [Getting Distributed](#getting-distributed)
  - [Remotes](#remotes)
  - [A central repository](#a-central-repository)
  - [Hosted Git](#hosted-git)
  - [This course: GitHub](#this-course-github)
  - [Add a SSH-key to Github](#add-a-ssh-key-to-github)
  - [Adding a remote](#adding-a-remote)
  - [The first push](#the-first-push)
  - [Cloning](#cloning)
  - [Sharing changes](#sharing-changes)
  - [Getting the latest changes](#getting-the-latest-changes)
  - [When pulling gets tricky](#when-pulling-gets-tricky)
  - [When pushing gets tricky](#when-pushing-gets-tricky)
- [Pull Requests](#pull-requests)
- [Closing Remarks](#closing-remarks)
  - [Remember](#remember)

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

### Committing with description

If you want to add more text you can create the commit with a text editor.

`$ git commit`

Though not required, it’s a good idea to begin the commit message with a single short (less than 50 characters).

The first row is the commit title. The description is written with a blank line between the title.

The description has no restriction on the length.

```git
The commit title (max 50 characters)

The description...
```

On some systems the default editor is vim. You can change editor with the following command:

`$ git config --global core.editor nano`

nano is a simple text editor within the terminal.

### Directories

If you use `add` with a file inside a directory, it will retain the directory structure

`$ git add src/main.js`

`$ git commit -m "Start le coding!"`

_There is only one .git at the top level of the repository, not one inside every directory._

_**Minor limitation**: Git doesn't support putting empty directories under version control._

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

## More on staging

### Unstaging

Put something into the staging area, then realize you really didn't want to do that?

To unstage everything (such that it will be considered as an untracked modification), just do:

`$ git reset`

You can also unstage an individual file:

`$ git reset -- README`

### Stage just some changes in a file

In the mess of development, you did two changes together and you want to commit just one of them.

Unfortunately, the two changes are in the same file.

Thankfully, git lets you stage just some of a file's changes!

`$ git add -p lib/Text/BarGraph.pm`

You can get a more graphical experience of this using:

`$ git gui`

Or any distributed gui client: https://git-scm.com/downloads/guis

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

## Branch Workflows

https://nvie.com/posts/a-successful-git-branching-model/

![](https://nvie.com/img/git-model@2x.png)

https://trunkbaseddevelopment.com/

![](https://trunkbaseddevelopment.com/trunk1c.png)

## The Stash

### What is the stash?

The stash is a place you can keep changes you're working on, but do not yet wish to commit.

Performing a stash pushes your current modifications onto the stash, and gives you a clean working tree.

`$ git stash`

You can get them back later by popping the stash:

`$ git stash pop`

### When to stash?

When you've been working on some changes, then realize you're in the wrong branch...

`$ git stash`

`$ git checkout the-right-branch`

`$ git stash pop`

When you realize you want to do something else (maybe a refactor) then resume your current work.

When you have uncommitted changes, but want to pull.

### Stashing just unstaged stuff

You've done two changes at once. You've managed to get just one of them staged for commit...then you realize a test now fails. You want to try them in isolation.

By default, stash will stash away anything you have already staged. However, you can tell it not to...

`$ git stash --keep-index`

(Note that the index is another name for the staging area. The two are used interchangeably.)

## Getting Distributed

### Remotes

Earlier on, it was mentioned that you can copy a Git repository, complete with history, by copying the contents of the .git directory.

_A remote is another copy of the current repository._

It may have some commits we don't have.

It may lack some commits we have.

_It should always be possible to trace back from the current latest commits locally and in the remote to a common ancestor._

### A central repository

The most common use of remotes is to set up a central repository.

```git
                Central
               Repository
              /    |    \
            /      |      \
          /        |        \
  Dave the     Tatyana the    Bob the
  Developer      Tester     Build Manager
```

It's the central repository because everyone agrees it is. To Git, it's just another copy.

### Hosted Git

There are a range of hosted Git services out there, which can host your central repository for you.

_GitHub_ is the largest today, offering free hosting for public repositories (used by thousands of open source projects) and private hosting for individuals and organizations.

_Azure devops_.

_Bitbucket_ is widely used in the Atlassian family. (Jira, Confluence, etc)

_Gitourious_ is a lesser known alternative; some projects have chosen it over GitHub.

_You can also host it yourself, with a bit of setup._

### This course: GitHub

We'll use repositories on GitHub for the purpose of practicing with remotes.

_All of the commands and techniques we learn are completely transferable to working with any other remote, whether hosted internally or elsewhere._

Having a GitHub account also opens the door to contributing to thousands of open source projects; it may well come in useful to you far beyond this course. ☺

### Add a SSH-key to Github

When working with a remote you always have to authorize the push/pull.

It can get rather tedious having to login with a password all the time.

The recommended thing to do is to add your public rsa_id key to your Github profile.

Create a rsa_id key with the command:

`ssh-keygen -t rsa -b 4096`

A couple of rsa files will be created in your .ssh directory in your home folder.

Mac/Linux: `~/.ssh`

Windows: `C:\Users\[your-user-name]\.ssh`

In your Github profile settings go to **SSH and GPG keys** and click the **"New SSH key"** button. Add your **id_rsa.pub** content here.

### Adding a remote

After creating a repository on GitHub, we need to tell our local Git repository about it by adding it as a remote.

Be sure that you copy the SSH url.

`$ git remote add origin git@github.com:user/repo.git`

Since you can have multiple remotes, you have to specify a name as well as the address.

The convention used by almost all Git users is to call the central repository remote _origin_.

### The first push

Pushing is taking commits we have locally and copying them to a remote.

If we have a new, empty, central repository then our first push should use the `-u` flag.

`$ git push -u origin master`

```git
Dave the Dev       Central
     * - HEAD         * - HEAD
     |   --------->   |
     *                *
```

### Cloning

The `clone` command is used when you want to get a local copy of a remote repository; it also sets up origin to point to the remote for you.

`$ git clone git@github.com:user/repo.git`

```git
  Central      Norbert the New Guy
     * - HEAD         * - HEAD
     |   --------->   |
     *                *
```

**You use clone once to get an initial copy of the remote; later you'll be using pull to synchronize.**

### Sharing changes

After you have done some work locally, you will have one or more commits that the central repository does not have.

```git
Dave the Dev       Central
     * - HEAD
     |
     *                * - HEAD
     |                |
     *                *
```

In this case, we need to copy the one extra, new commit over to the central server.

The push command is used to do this:

`$ git push origin master`

```git
Dave the Dev       Central
     * - HEAD         * - HEAD
     |                |
     *    ------->    *
     |                |
     *                *
```

You don't need to tell Git which commit(s) it needs to copy. It always works that out for you. ☺

### Getting the latest changes

At this point, the central repository has a commit that Norbert the New Guy doesn't have in his local copy of the repository.

```git
  Central      Norbert the New Guy
     * - HEAD
     |
     *                * - HEAD
     |                |
     *                *
```

We need to copy that new commit from the central repository into the local copy.

The `pull` command is used to `fetch` the latest changes and `merge` them into our local copy.

`$ git pull`

```git
  Central      Norbert the New Guy
     * - HEAD         * - HEAD
     |                |
     *    ------->    *
     |                |
     *                *
```

`pull` really just calls two other commands for us:

`fetch`
This command fetches all of the changes from a remote that we don't know about locally.

`merge`
We already saw this one! ☺

The exact same mechanism we used to merge local branches is also used to merge commits from a remote. Neat, huh? ☺

### When pulling gets tricky

Since a pull involves a merge, all of the cases of merging we saw earlier can come up again.

`Fast-forward`
If you have no local commits, you always get a straightforward fast-forward merge.

`Merge Commit`
When you have local commits and also bring in remote ones, the DAG looks just as it does in a branch situation. A merge commit is needed. Sometimes, manual intervention may be needed.

### When pushing gets tricky

Sometimes, both you and the remote repository may have extra commits.

```git
  Central      Norbert the New Guy
     * - HEAD         * - HEAD
     |                |
     *                *
     |                |
     *                *
     |                |
     *                *
```

If Norbert tries to push, it will fail.

`$ git push origin master`

```git
  Central      Norbert the New Guy
     * - HEAD         * - HEAD
     |                |
     *                *
     |                |
     *    <---X---    *
     |                |
     *                *
```

The solution is to pull first.

`$ git pull`

This will result in a merge commit being created locally; we then push it along with our commit.

`$ git push origin master`

The underlying principle here is that all merging takes place locally, and you push the results of the merge.

## Pull Requests

When a team grows it's a bad idea to merge branches directly.

Unchecked code might get merged, and a lot conflicts will arise.

A pull request is a merge in waiting - waiting to be reviewed and approved before merging.

This pull request is a great place to discuss code practices, and highlight parts that's good, or might add technical dept.

It should have a good description, and if working with a gui component it would be good with a screenshot of the implemented feature.

It's also motivating to check off pull requests as complete, and it helps notify others that changes are inbounds.

## Closing Remarks

**Branching is lightweight, natural and cheap** - both in terms of branch creation and especially thanks to efficient merging.

Working locally makes things fast.

**More complex workflows can be implemented** since all copies of a repository are on an equal footing; you give them their place and value.

### Remember

In the end, it's all about commits and the DAG.

**Branches are a central part of effective Git usage**. It's better to create a branch you then merge right away, than to not create one and wish you had.

**Rebasing and amending commits** are powerful techniques - but you must only apply them to local history that has not been shared.

Look at your desired development process, and **shape your Git usage** around it - it's malleable!
