---
layout: post
title: "For the Last Time: Git Stores Snapshots Not Diffs"
date: 2014-10-14 10:15:11 -0700
comments: true
categories: Git
---

I have this discussion every month or two, so it's finally time to write a blog post I can point people to instead.
**Git stores a snapshot of the entire file, not a diff**

This is an understandable point of confusion, because most output from Git is in the form of diffs.
Additionally, many other SCMs store changes as diffs instead of snapshots.
However, Git stores an entire snapshot of each modified file.

If you want to see an explanation from someone much smarter than me, check out [this video](https://www.youtube.com/watch?v=rALm7BCCY-0#t=6m28s).

Illustrating Snapshots
---

The easiest way to show how Git stores snapshots is create a project and show the relative sizes of the .git object folder.
