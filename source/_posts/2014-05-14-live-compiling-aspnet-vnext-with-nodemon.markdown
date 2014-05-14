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

Now that nodemon is installed, you need to figure out where the vNext's "K" command is located.
It should be on your path, but for some reason nodemon can't find it without an absolute path.

In cmd run: `where K`

In PowerShell run: `gcm K | select-object -ExpandProperty Definition`

Unfortunately, I don't think Git Bash is supported yet, since K.cmd doesn't seem to have a BASH counterpart.

Copy the full path output by the previous command.
Now paste it into the following Nodemon command.

`nodemon --exec "<path to K.cmd> run" -e cs,json`

This tells Nodemon to watch for changes to *.cs and *.json files and to restart "K run" when changes are detected.
Replace "run" with "web" as appropriate.

A concrete example of this setup might look like this:

```
C:\Users\me\aspnet-home\samples\ConsoleApp>where K
C:\Users\me\.kre\packages\KRE-svr50-x86.0.1-alpha-build-0421\bin\k.cmd

C:\Users\me\aspnet-home\samples\ConsoleApp>nodemon --exec "C:\Users\me\.kre\packages\KRE-svr50-x86.0.1-alpha-build-0421\bin\k.cmd run" -e cs,json
```

This functionality may already exist in ASP.NET vNext, and I just haven't found it.
If so, [let me know](https://twitter.com/itsananderson).
If not, it's hopefully on the roadmap.
For now this is a handy workaround.