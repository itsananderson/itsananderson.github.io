---
layout: post
title: "Netbeans FTP File Permissions Fix"
date: 2010-03-14 22:04:51 -0700
comments: true
categories: Netbeans
---

I've been trying out Netbeans' "PHP Application from Remote Server" feature this weekend. The idea is that when you save a file, it uploads that file to a remote server where it can be run.

The problem I ran into is that when Netbeans uploads that file and overwrites the old version, the permissions on the file change. I did some searching and it sounds like Netbeans doesn't set permissions on the file, which means it just takes the server default. This doesn't make much sense (should keep permissions of previous file) and for me it meant that the permissions were set so low that I couldn't run the PHP scripts I was uploading.

It's simple enough to change the permissions on those files.

```sh
chmod -R 777 /path/to/files
```

But you have to run this command *every* time you make a change. This is a royal pain when you're making a lot of small changes (like when you're trying to track down a bug for example).

One solution might be to try hooking into the ftp server daemon to run that command every time something is committed, but that sounded like too much work. I came up with a better solution.

Instead, I decided to just run that command in a loop. The following scripts runs infinitely (until execution is stopped) and waits 1 second, then runs the permission command.

```sh
while (true); do
    sleep 1
    chmod -R 777 /path/to/files
done;
```

If you want to be able to do anything else in the command line while this is running, it's probably a good idea to run it in a screen.

Also, you can end the execution of this script with CTRL+C. Otherwise it will continue executing forever.