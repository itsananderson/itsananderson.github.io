---
layout: post
title: "Inspecting Git Traffic in Fiddler"
date: 2014-05-07 11:32:26 -0700
comments: true
categories: Git Fiddler 
---

If you ever need to inspect Git traffic to diagnose problems, or are just curious about the internal workings of Git, it's pretty easy to make Git send traffic through Fiddler.

```
git config --global http.proxy 127.0.0.1:8888
git config --global http.sslVerify false
```

This tells Git to use Fiddler as the proxy, and to turn off SSL certificate verification (so it doesn't balk at Fiddler's cert).

Make sure Fiddler's actually running, or Git won't be able to connect.

If you just want to use Fiddler for a single repository, run the commands from within that repository without the `--global` flag. Obviously this work for the `git clone` command, so you'll have to use a global configuration if you want to inspect that.

When you're done, make sure you undo your changes:

```
git config --global --unset http.proxy
git config --global --unset http.sslVerify
```

Again, remove the `--global` flag if you're just inspecting traffic for a single repository.
