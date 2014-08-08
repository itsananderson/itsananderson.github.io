---
layout: post
title: "Live Compiling ASP.NET vNext With nodemon"
date: 2014-05-14 12:47:24 -0700
comments: true
categories: ASP.NET Node
---

Something cool that Scott Hanselman talked about in his [blog post on vNext](http://www.hanselman.com/blog/IntroducingASPNETVNext.aspx)
was the ability to save a C# file in Visual Studio and view the change in the browser without manually rebuilding.

After spending a little time messing around with the [newly released code](https://github.com/aspnet), I wanted similar functionality when editing C# files outside Visual Studio.
I didn't see a documented way to do that, but there's an easy workaround if you have Node installed.

First install the "nodemon" package.
This package monitors a directory for file chanages and respawns a child process whenever anything changes.

`npm install -g nodemon`

Now that nodemon is installed, you can use it to live-rerun your app.

`nodemon --exec "K.cmd web" -e cs,json`

This tells Nodemon to watch for changes to *.cs and *.json files and to restart "K.cmd web" when changes are detected.
Replace "web" with "run" or whatever startup command is configured for your project.

Note that if you're running in Git Bash on Windows or on a Linux/OSX machine, the --exec command will probably be either `K web`, `K.cmd web`, or `K.sh web`.

![Live re-compiling with nodemon](//itsananderson.blob.core.windows.net/post-images/nodemon-k.png)

This functionality is [on the way for ASP.NET vNext](https://github.com/aspnet/Home/issues/22#issuecomment-43148021), but for now this is a handy workaround.
