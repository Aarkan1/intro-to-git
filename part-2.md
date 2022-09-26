Teacher outlines Part 2

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
- [The Wonder of Rebasing](#the-wonder-of-rebasing)
  - [Re-writing history](#re-writing-history)
  - [Rebasing](#rebasing)
  - [Pulling with rebasing](#pulling-with-rebasing)
  - [Amending commits](#amending-commits)
- [Branch Workflows](#branch-workflows)
- [The Stash](#the-stash)
  - [What is the stash?](#what-is-the-stash)
  - [When to stash?](#when-to-stash)
  - [Stashing just unstaged stuff](#stashing-just-unstaged-stuff)
- [More on staging (aka. the index)](#more-on-staging-aka-the-index)
  - [Unstaging](#unstaging)
  - [Stage just some changes in a file](#stage-just-some-changes-in-a-file)
- [Pull Requests](#pull-requests)
- [Closing Remarks](#closing-remarks)
  - [Remember](#remember)

---

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

In your Github profile settings go to **SSH and GPG keys** and click the **"New SSH key"** button. Add your **rsa_id.pub** content here.

### Adding a remote

After creating a repository on GitHub, we need to tell our local Git repository about it by adding it as a remote.

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

## The Wonder of Rebasing

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

### Rebasing

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

## More on staging (aka. the index)

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

## Pull Requests

When a team grows it's a bad idea to merge branches directly.

Unchecked code might get merged, and a lot conflicts will arise.

A pull request is a merge in waiting - waiting to be reviewed and approved before merging.

This pull request is a great place to discuss code practices, and highlight parts that's good, or might add technical dept.

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
