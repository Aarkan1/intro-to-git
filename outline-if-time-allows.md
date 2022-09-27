Teacher outlines

This part is optional if time allows.

- [Rebasing](#rebasing)
  - [Re-writing history](#re-writing-history)
  - [git rebase](#git-rebase)
  - [Pulling with rebasing](#pulling-with-rebasing)
  - [Amending commits](#amending-commits)
- [A Peek Inside Git](#a-peek-inside-git)
  - [SHA-1 Hashes](#sha-1-hashes)
  - [Writing SHA-1 Hashes](#writing-sha-1-hashes)
  - [Why not a revision number?](#why-not-a-revision-number)
  - [The DAG](#the-dag)
  - [And that's Git!](#and-thats-git)
- [Tags](#tags)
  - [Two types of tag](#two-types-of-tag)
  - [Creating tags](#creating-tags)
  - [Working with tags](#working-with-tags)
  - [describe](#describe)
- [Random Useful Stuff](#random-useful-stuff)
  - [Branch creation tips](#branch-creation-tips)
  - [Some more options to log](#some-more-options-to-log)
  - [See a file at a particular commit](#see-a-file-at-a-particular-commit)
  - [blame](#blame)
  - [Get a ZIP](#get-a-zip)
- [A Little History](#a-little-history)
  - [RCS](#rcs)
  - [CVS](#cvs)
  - [Subversion (aka. SVN)](#subversion-aka-svn)
  - [DVCS: have your cake and eat it](#dvcs-have-your-cake-and-eat-it)
  - [So really, distributed means...](#so-really-distributed-means)
  - [Branching and merging](#branching-and-merging)
  - [Branches in the DVCS world](#branches-in-the-dvcs-world)
- [Going Deeper Into Git](#going-deeper-into-git)
  - [Commits, trees and blobs](#commits-trees-and-blobs)
  - [Porcelain and plumbing](#porcelain-and-plumbing)
  - [Exploring with cat-file](#exploring-with-cat-file)

---

## Rebasing

### Re-writing history

When working with Git and you have remotes, your version history falls into two categories.

```git
    *       History consisting of
    |       commits you have made
    *       locally, but have never
    |       pushed to a remote
    *  *
_____\_|_________________________________
       *
       |    History that you share with
       *    others, e.g. that you have
       |    pushed to or pulled from a remote.
       *
```

Since nobody has seen your local commits, you are free to manipulate that part of the history without causing any problems.

**Very Important Warning**
You must be very careful to never change history that you have already shared. **The big hint you've got it wrong is that a push will fail, and Git will suggest using --force. DO NOT DO THIS**; you will cause problems for others.

### git rebase

You started a branch to work on a feature, and have made a few commits. Then you switched back to master to do a bug fix. You pushed the bug fix, and now return to your feature branch.

```git
                            *
Your work on the feature    |       On the other hand, the bug
branch is local -           *       fix is now pushed;
you did not                 |       it is shared history
push it to a remote         *   *
                              \ |
                                *
```

A couple of things are now less than ideal...

Your feature branch is missing the bug fix ☹
You'd really like to have the bug fix in your branch too; it may affect your feature, and you'd like to be able to test the two in combination.

You won't be able to do a fast-forward merge ☹
This isn't a huge problem, but merge commits do create a little clutter in the version history; many Git users prefer fast-forward merges.

The `rebase` command can help here. In the feature branch, we can run:

`$ git rebase master`

In a rebase, Git takes the commits in your branch, then "replays" them as if the branch had started at the master branch's current commit, so it has all master does and can fast-forward!

```git
 before          after
                   *
                   |
    *              *
    |              |
    *              *
    |                \
    *   *             *
      \ |             |
        *             *
```

### Pulling with rebasing

We've seen that merge commits can also appear when you have local commits, then pull commits from a remote repository.

It is often desirable to avoid these merge commits; pulling is such a common scenario that this will quickly clutter the history.

Thankfully, it's as simple as:

`$ git pull --rebase`

### Amending commits

Ever made a commit...then instantly realized it had a mistake - perhaps even in the commit message?

Provided you did not push the commit, this is easy to fix locally; correct any files, and then use the amend flag:

`$ git commit --amend -m "New message"`

This will take the previous commit, incorporate the current changes into it, and use the new commit message you specify also; omit the commit message to be prompted to stick with the original one.

If you want to use the previous message you can use the `no-edit` flag:

`$ git commit --amend --no-edit`

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

## Tags

### Two types of tag

We use tags to give certain commits nice names. (For example when making a release.)

There are two types of tags:

`Lightweight tags`

Simple SHA-1 pointers to a particular commit.

`Annotated tags`

Contain a commit message, a GPG signature, or both. These are actually a new, but simple, kind of object.

### Creating tags

Both types of tag are created using the tag command; the difference is just in how you use it.

To create a lightweight tag, just give it a name:

`$ git tag v3.0`

If you add a commit message, you get an annotated tag:

`$ git tag -m "Version 3.0." v3.0`

### Working with tags

Tags are not automatically pushed to a remote. This is because a tag that has been shared is not meant to change again (by social protocol). To push tags, do:

`$ git push origin --tags`

You can also get a list of all tags, or delete tags (remember not to do this to pushed ones)

`$ git tag`

`$ git tag -d v1.1`

### describe

We know that Git gives commits SHA-1 hashes rather than version numbers...but sometimes you might really want something a little more numeric.

The describe command produces a commit descriptor that includes the number of commits since the last tag.

`$ git describe`

The output is something like:

`2012.10-131-gca9c30d`

## Random Useful Stuff

### Branch creation tips

We already saw that you can create a branch with the branch command, but you can also create a new branch and check it out all in one go!

`$ git checkout -b new-feature`

You can also (either to this or to the branch command) specify a commit to start the branch from (the default is HEAD, but you may want a branch based on a tag or a SHA-1):

`$ git checkout -b hotfix v1.1`

`$ git checkout -b hotfix 7062ca7`

### Some more options to log

You don't have to just settle for the latest n commits when you do a log! Here's some other options...

-   `--since="2 weeks ago"`
-   `--until="2 weeks ago"`
-   `--grep=pattern` (matches on the commit message)
-   `--committer=pattern`
-   `--author=pattern`
-   `--no-merges` (only commits with one parent)

Like the graph in gitk? Get an ASCII art version!

`git log --graph`

### See a file at a particular commit

Sometimes it is useful to view a file at a certain commit, without having to check out that commit

`$ git show 7062ca7:src/main.js`

Here we've specified a SHA-1, but you can use any commit identifier...

-   A SHA-1
-   A branch name
-   A tag name

### blame

Want to understand the origins of lines of a file?

The blame command annotates every line with the last commit that touched it, the date the commit was made, and the author that changed it.

`$ git blame src/main.js`

### Get a ZIP

Ever wanted to get a ZIP of the whole repository at a certain revision? There's a command for that!

`$ git archive --format=zip HEAD > foo.zip`

In place of HEAD, you could also use a SHA-1, branch name, tag name... Doing this with a tag means you have a quick and easy way to give somebody a copy of the repository files as they were at a certain release!

You may also specify `--format=tar` to get a tarball.

## A Little History

### RCS

Amongst the earliest implementations of version control, yet still in some use today!

Only works on _individual files_; mostly useful for individual documents, scripts and configuration files rather than large software projects

_No central repository; all operations are local_

Had branching support, but considered awkward by many users - and again, it was only branching at the level of an individual file

### CVS

Developed in the mid-80s, and was highly popular well into the late 90s; still in active use today

_Client-server system_; server holds the entire history, and clients have a single working version

Can _version entire sets of files_ - but version number is still per file, commits are not atomic and renaming/moving is not versioned

_Branches are expensive_; the assumption was that they would be rare, short-lived or historical

### Subversion (aka. SVN)

Aims to be familiar to CVS users, but fixing longstanding issues and supplying new features.

In widespread use today.

Similar _client-server approach_ to CVS, however _commits are atomic commits_ and _version number is for the whole repository_, not individual files.

_Branches and tags both implemented by copying_, with the result that they are _still expensive_; worse, tags can be committed to!

### DVCS: have your cake and eat it

| Feature                                                            | Local (e.g. RCS) | Centralized (e.g. SVN) | Distributed (e.g. Git) |
| ------------------------------------------------------------------ | ---------------- | ---------------------- | ---------------------- |
| Full history available locally                                     | YES              | NO                     | YES                    |
| Can perform all operations locally                                 | YES              | NO                     | YES                    |
| Support having a central repository to share work                  | NO               | YES                    | YES                    |
| Support complex topologies, e.g. repositories for dev. and testing | NO               | NO                     | YES                    |

### So really, distributed means...

-   Everybody has the full version history locally and can manipulate it as they wish
-   We can efficiently move one or more commits between different copies of a repository

### Branching and merging

Many development workflows depend upon _branching and merging_.

In just about all version control systems, _creating a branch is easy_ - though it may be slow.
(e.g. SVN wants to make a complete copy)

Generally, the _difficult part is merging_.

_With Git, branch creation is instant, and it works hard for you on merging, usually only leaving you conflicts that genuinely need human attention._

### Branches in the DVCS world

If you have never worked with a DVCS before...

...please now forget everything you think you know about branches! ☺

Especially, stop believing all of the following:

Branches are expensive
Only create a branch if you really need it
Merging is to be avoided because it's hard

Effective Git users work with branches every day!

## Going Deeper Into Git

### Commits, trees and blobs

I told you that Git just had commits forming a DAG. It was a slight lie. There are also trees and blobs.

A blob corresponds to a file.

A tree corresponds to a directory. Trees can contain blobs and other trees.

### Porcelain and plumbing

Git commands divide up just like a bathroom...

The _porcelain_ commands are what normal users interact with. We only used these so far.

The _plumbing_ commands are lower level. The porcelain commands are built using them.

### Exploring with cat-file

We've talked about `show`, which is a _porcelain_ command that lets us look at the contents of a commit. But really, it's doing quite a lot of work for us.

So see the raw commit object, we can use cat-file, which is one of the plumbing commands.

`$ git cat-file -p 0487d7`

From this we can see that the commit really does point off to a tree, and then go exploring the tree itself...
