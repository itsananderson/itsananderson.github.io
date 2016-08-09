---
layout: post
title: "Docker for Windows: Interactive Sessions In MinTTY Git Bash"
date: 2016-08-08 19:01:41 -0700
comments: true
categories: Docker
---

I recently installed Docker for Windows on my laptop. When I tried running the default `docker run -it ubuntu bash` demo from Git Bash, I ran into an issue I hadn't seen before.

```
$ docker run -it ubuntu bash
the input device is not a TTY. If you are using mintty, try prefixing the command with 'winpty'
```

As it turns out, following the instructions and prefixing the command with `winpty` does work

```
$ winpty docker run -it ubuntu bash
root@88448b75631d:/#
```

However, I'm never satisfied with just getting around an issue, so I did some more digging.
Turns out the issue here is the use of MinTTY to host the Git Bash prompt.
If you've installed Git for Windows, you'll recall the following configuration window.

![Git Bash MinTTY configuration](https://itsananderson.blob.core.windows.net/post-images/git-bash-mintty.png)

If you select the "Use MinTTY" option, your Bash pompt will be hosted in the MinTTY terminal emulator, rather than the CMD console that ships with Windows.
The MinTTY terminal emulator isn't compatible with Windows console programs unless you prefix your commands with `winpty`.

Personally, I prefer the default CMD console, especially in Windows 10 where features like window resizing and text selection are drastically improved.
If you want to go back to that mode, simply re-run the Git for Windows installer and chose the non-MinTTY option.
If you want to stick with MinTTY, you just need to prefix your interactive Docker (and Python, and Node, and ...) commands with `winpty`.
