---
layout: post
title: "Docker for Windows: Sharing Host Volumes"
date: 2016-07-30 21:15:38 -0700
comments: true
categories: Docker
---

I've been playing with the new "Docker for Windows" tool recently, and I wanted to share a slightly obscure issue I ran across.

Docker lets you share volumes between your containers and your host machine. This is handy during development time, when you want to be able to quickly iterate without having to continually rebuild your container.

### The Suspicious Empty Directory

When I first tried sharing a volume, I typed this into PowerShell:

```
> dir c:\docker


    Directory: C:\docker


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----        7/30/2016   9:47 PM              8 test.txt

> docker run -it -v c:/docker:/host ubuntu bash
root@333a10babe3a:/# ls /host
root@333a10babe3a:/#
```

Hmm... As you can see, the `c:\docker` folder has a test file, but it doesn't show up in the Docker container.
After some head scratching, I figured out the missing piece: Out of the box, Docker isn't configured with permissions to share your drives with your containers.

### Giving Docker Access

To give Docker access to your computer's drives, right click on the Docker icon in your taskbar, then click "Settings..."

![](https://itsananderson.blob.core.windows.net/post-images/docker-settings.gif)

Under the "Shared Drives" section, check the drives you'd like to share, then click "Apply"

![](https://itsananderson.blob.core.windows.net/post-images/docker-shared-drives.png)

Docker will ask you for credentials, which it uses to access the drives.

![](https://itsananderson.blob.core.windows.net/post-images/docker-shared-drives-credentials.png)

(Note that if you log into your computer with a Windows Live account, your username should be something like "MicrosoftAccount\you&#64;live.com")

Once you save your credentials, you should be able to share volumes from your host computer to your containers.

```
>docker run -it -v c:/docker:/host ubuntu bash
root@00aca8c2f880:/# ls /host
test.txt
root@00aca8c2f880:/#
```
