---
layout: post
title: "Git Concepts: Branches, Forks, and Pull Requests"
date: 2014-05-09 10:57:58 -0700
comments: true
categories: Git
---

My team recently started migrating our source control from TFVC to Git (yay!).
There's been some confusion about a few Git and DVCS related terms.
If you're also confused, don't worry. You're not the only one.

<iframe class="youtube-player" type="text/html" width="640" height="385" src="https://www.youtube.com/embed/k2vJNNAQZlg" allowfullscreen frameborder="0">
</iframe>

Let's talk about some basic Git concepts.

Branches
--------

Branches are a core concept in Git.
Unlike centralized version control, branches in Git don't show up as folders in the file system.
Instead, your project folder only contains the contents of one branch, which you "checkout" into your working directory.

Internally, branches are basically a pointer to a commit, designed to move forward when new commits are created.

See [this page on git branches](http://www.git-scm.com/book/en/Git-Branching-Basic-Branching-and-Merging) for more details.

Forks
-----

Forking is a concept that exists outside Git.
In the old days, forking an open source project meant splitting off from the original project, either to continue development or move in a different direction.

These days a fork is just an implementation detail of several open source code hosting websites.
If I want to introduce a change to an open source project, I obviously can't just edit the repository directly.
Instead, what I have to do is fork the repository, which creates a copy of that repository that belongs to me.
I can then modify that fork and, if desired, propose those changes back to the original repository.

GitHub has a pretty good [article on forking repositories](https://help.github.com/articles/fork-a-repo).
It also talks about creating pull requests.

Pull Requests
-------------

One of the most easily misunderstood concepts in Git is the pull request.
Part of the reason for the confusion is that in most current pull request workflows, nobody ever "pulls" changes.
"Merge request" would be a more accurate term.

When you create a pull request on an open source project, you're usually asking that changes from a branch in your repository be merged with a branch on another repository.
In most cases, your repository is a fork of the other repository, and you're requesting to merge your changes into their master branch.

Pull requests can also be used as a code review workflow within a single repository.
I've done this for some of my own open source projects.
It works really well for teams that are working together on a project.

If you're not already doing your feature work in a branch, you should learn to [commit good](http://willi.am/blog/2014/05/06/git-for-devs-who-cant-commit-good/).
Once you're doing your work in a branch, pull requests become second nature.
As soon as you're at a point where you're not embarassed by your code, create a pull request.
You can continue pushing new changes to your branch for reviewers to see.
By starting the feedback loop as early as possible, your peers can help catch issues that you'd otherwise have to spend a long time undoing.

The other thing pull requests for team projects enables is multiple collaborators.
Most other code review tools only belong to one person.
This is a hassle if two people have collaborated on a feature.
With pull requests, many collaborators can push to a single branch, and all changes can be reviewed as a single unit.

Once again, GitHub has a good [article on pull requests](https://help.github.com/articles/using-pull-requests).
This one is very specific to GitHub's pull request workflow.
