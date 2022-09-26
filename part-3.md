Teacher outlines Part 3

This part is optional if time allows.

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
