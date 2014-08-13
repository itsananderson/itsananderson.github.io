---
layout: post
title: "The Dark Side of the Force Push"
date: 2014-08-12 23:26:08 -0700
comments: true
categories: Git
---

Let's talk about force pushing in Git.
We'll cover when you should do it, when you shouldn't, and how to do it safely.

TL;DR

Do this:

![Force Push Droids](http://itsananderson.blob.core.windows.net/post-images/obi-wan-droids.gif)

Not this:

![Force Push Fail](http://itsananderson.blob.core.windows.net/post-images/obi-wan-hates.gif)

Pushing Changes
---

When you push changes to a remote server, Git prevents you from pushing your changes if the remote has changes you're missing.
This feature is designed to prevent data loss.
If you run into this, you'll likely see a message similar to this:

```
$ git push origin my-branch
To git@github.com:itsananderson/foo.git
 ! [rejected]        my-branch -> my-branch (non-fast-forward)
error: failed to push some refs to 'git@github.com:itsananderson/foo.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

99% of the time, the appropriate solution to this message is to `git pull` to fetch changes from the server,
then `git push origin my-branch` to push your updated branch to the server.

Rebasing
---

The only real exception to the "always pull, then push" rule, is rebasing.
When you rebase, you're creating a copy of your commit history.
Git treats this new copy of history as a completely different set of commits (because it is).

I won't go into deep detail about rebasing, because it's been covered many times before.
GitHub has a good [tutorial on rebasing](https://help.github.com/articles/using-git-rebase).
If you want some [high-level concept slides on rebasing](http://willi.am/git-fundamentals-rebasing/), I wrote some for a team presentation.

Point is, when you rebase a branch that you've pushed to a remote server, your copy of the branch and the server's copy will be considered completely distinct.
This means when you try to push, the remote server will reject your changes because they're not a fast-forward.
However, if you `git pull`, you'll end up with two copies of the branch which are then merged with a merge commit.
In addition to muddling your commit history, this will re-introduce whatever you were trying to change with your rebase.

In this very specific case, the correct solution *might* be to `git push origin my-branch -f`.

Force Push Basics
---

So you have a branch that you've rebased, and you want to force push it to the remote server.

![Will Smith Force Push](http://itsananderson.blob.core.windows.net/post-images/force-push-will-smith.gif)

First, make sure nobody's pulled down your branch and done local work off of it.
If they have, your force push may make it difficult for them to reconcile their changes with yours.

If you're working on a team and need to rebase a shared branch, here are the steps:

1. Make sure your team has committed and pushed any pending changes
1. Ask your team to pause work on that branch temporarily
1. Make sure you have the latest changes for that branch (git pull)
1. Rebase, then `git push origin <yourbranch> -f`
1. Have your team fix up their local branches with `git checkout <yourbranch>`, `git fetch` and `git reset --hard origin/<yourbranch>`

As you can see, this is a complicated process.
It requires team coordination, and potentially blocks team members from doing work while you rebase.
As a general rule, avoid rebasing or otherwise modifying history on pushed branches unless you've accidentally published [sensitive data](https://help.github.com/articles/remove-sensitive-data).

If you're working alone, the process is a little simpler.
Simply rebase and then `git push origin <yourbranch> -f`.
Keep in mind, however, that you may still cause headaches for other people if you rebase on a public GitHub (Bitbucket, etc.) repository.

Aside: Understanding Remote Tracking Branches
---

Git uses the concept of remote tracking branches (or branch upstreams) to help you associate your local branches with the branches on remote servers.
If you've ever seen this message, that's what it's talking about: `Branch my-branch set up to track remote branch my-branch origin.`

Additionally, if you've seen this message when you ran `git status`, it means your branch is tracking a remote branch:

```
On branch my-branch
Your branch and 'origin/my-branch' have diverged,
and have 5 and 2 different commit each, respectively.
```

Finally, when you run `git pull` or `git push`, Git can use the remote tracking branch to know which remote branch to interact with.
This keeps you from having to type `git pull origin my-branch` every time.

There's a blog post with more info on [remote tracking branches and upstreams](http://felipec.wordpress.com/2013/09/01/advanced-git-concepts-the-upstream-tracking-branch/).

Force Push Pitfalls
---

When you start force pushing, there are a few pitfalls you should be aware of.

![Yoda Force Push](http://itsananderson.blob.core.windows.net/post-images/force-push-yoda.gif)


### Overwriting Changes

Before you force push a branch, double check that nobody has pushed new changes to that branch.
If they have, and you don't pull them in, you'll overwrite them when you force push.

With online source control, you can just browse the repository and check the commit history for your branch.

If you don't have a web UI, you can also check directly in the source.

1. Run `git fetch` to pull down the latest changes to your machine
1. Run `git log origin/<yourbranch>` to remote branch's log
1. If you don't see any new commits, it's probably safe to proceed (ignoring developer race conditions)
1. If you see new changes, resolve them into your local branch before you force push

As with software, you can run into a race condition.
If somebody pushes changes between when you fetch and when you push, you may still overwrite them.
To avoid this, communicate with your team.
A simple "don't push anything on <yourbranch> for a minute", followed up by an "all clear" should do the trick.

### Overwriting Other Branches

When you push and pull, Git can use remote tracking branches (see above) to infer which remote branch you want to push/pull from.
This is generally ok for normal push/pull activity, but when you force push, it can be dangerous.

By default, Git responds to `git push` by pushing *all* branches that exist on both your local machine and the remote server.
This means, if you have commits on master and my-feature-branch, and you run `git push`, it will push your changes from both branches at once.

In Git 2.0, this behavior was changed to only push the current branch, but older versions still have this unexpected behavior.
To fix it, run the following command:

`git config --global push.default simple`

Now if you run `git push`, it will only try to push your current branch.

The default push behavior becomes especially dangerous when you force push.
If, for example, you run `git push -f`, and your master branch is behind the remote server's master,
you may accidentally remove commits from master.

For this reason, I recommend two things:

1. Configure push.default with the safer `simple` setting
1. Always explicitly state the remote and branch name when you force push: `git push -f origin my-feature-branch`

In Conclusion
---

Hopefully this gives you a good overview of the how/why/when of force pushing.

If you haven't already, be sure to read the section on force push pitfalls.
They're important to understand, and they're the main reason I wrote this post.
