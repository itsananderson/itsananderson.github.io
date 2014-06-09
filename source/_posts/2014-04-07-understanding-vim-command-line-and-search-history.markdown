---
layout: post
title: "Understanding Vim Command Line and Search History"
date: 2014-04-07 22:48:12 -0700
comments: true
categories: Vim
---

If you're a Vim user like me, you've likely found yourself staring at a view like this and wondered:

#### "How'd I get here, and how do I get away"

{% img /images/post-images/vim-command-line-history.png %}

Don't dispair. You've discovered Vim's command line history. This is the list of commands you've run. They're stored in `~/.viminfo` or `~/_viminfo`, depending on your system configuration

There are actually two modes you can get into:
Command Line History, and Search History.
We'll cover both

If you didn't want to get into your command/search history, you can easily get back to normal mode by hitting `CTRL+C` twice

Command Line History
--------------------

Command line history is activated by typing `q:`. I usually find myself in this mode after trying to type `:q` too quickly

This is actually a pretty useful tool.
You're basically opening a file that contains your command history, so you can treat it like you would a file you have opened in Vim.

Start by navigating through the commands.

* You can use `j` and `k` to move down and up.
* You can search through the commands with `/<searchtext>`

Once you find the command you want to run, simply hit `ENTER` from "Normal" mode to run that command on the current file

Search History
--------------

Search history is activated by typing `q/`.

This is also a useful tool, though not as useful as command line history, in my opinion

Just like command line history, you can navigate through the history just like a regular file, and hit `ENTER` with the cursor over the desired search to perform that search on the current document

Now You're Better at Vim
---

When I figured out what these views meant, it took a scary "how do I get out of this mode" moment, and turned it into a "this is useful" moment.
Hopefully after reading this post, you'll feel the same way!