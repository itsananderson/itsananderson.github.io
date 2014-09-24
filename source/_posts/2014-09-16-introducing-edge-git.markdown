---
layout: post
title: "Introducing Edge-Git"
date: 2014-09-16 22:50:30 -0700
comments: true
categories: Git Node
---

I've recently been frustrated by the lack of functional Node bindings for [libgit2](https://libgit2.github.com/).
The most prominent projects are [node-gitteh](https://github.com/libgit2/node-gitteh) and [nodegit]().
Unfortunately both repositories are severely lacking in features and documentation.
I couldn't even install node-gitteh on Windows, and I spent an unsuccessful evening trying to get nodegit to do something as simple as fetching changes.

Eventually I came to the conclusion that no libgit2 bindings for Node are functional enough for my use.

I'd used [LibGit2Sharp](https://github.com/libgit2/libgit2sharp) for a .NET project in the past.
It's an amazingly high-quality project.
While the documentation is sparse, the copious unit tests make it easy to figure out how to accomplish different tasks.

I'd read about [Edge.js](http://tjanczuk.github.io/edge/), which lets you interop between .NET and Node, but hadn't actually done anything with it.
Since I had a seemingly perfect use case, I decided to dive in.

About a week later, I have a very basic initial release of [edge-git.](https://www.npmjs.org/package/edge-git)
About all you can do at this point is init, clone, and query branches/commits, but I'm steadily adding more bindings to the existing LibGit2Sharp functionality.
