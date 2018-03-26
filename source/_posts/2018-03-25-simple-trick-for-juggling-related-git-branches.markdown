---
layout: post
title: "Simple Trick For Juggling Related Git Branches"
date: 2018-03-25 13:47:12 -0700
comments: true
categories: Git
---

At my job, it's not uncommon to work on a change that builds on another change which is still in code review. Sometimes a coworker is adding a component that I want to consume, and other times I'm just breaking my own work into multiple stages to simplify code review.

We squash commit to master rather than merging development branches, so whenever the code I depend on lands in master, I do an interactive rebase. This removes the unsquashed commits and cleans up my development branch. I also do a similar rebase if the code I'm building on is updated while it's still in development.

In either case, it can sometimes be tedious to figure out which commits in my branch are mine, and which commits I'm building on. This is especially annoying if I'm building on my own branch, because then all the commit authors looks the same. To solve this, I've started leaving a marker for myself in the form of an empy commit to delineate my changes.


```
git commit --allow-empty -m "============="
```

This way when I rebase, I get a nice little marker in the interactive rebase screen that tells me where to start throwing away commits.

<img src="https://itsananderson.blob.core.windows.net/post-images/rebase-with-marker3.gif" style="border: 1px solid #aaa" />

Because the commit is empty, the rebase command will automatically ignore it by commenting out the commit. If I'm done with the marker, I'll remove it or just leave it commented out. Otherwise, I uncomment that line to keep the marker commit around after the rebase. Either way, since we squash commit to master, the empty commit eventually disappears and doesn't clutter up my Git history.

This is also fairly trivial to even if you have an in-progress branch that you later decide to rebase onto another in-progress branch. Here's how I do it:

<img src="https://itsananderson.blob.core.windows.net/post-images/git-rebase-marker2.gif" style="border: 1px solid #aaa" />

That's pretty much it. I've found this to be a helpful way to leave myself breadcrumbs, especially when I haven't touched a branch for some time.
