---
layout: post
title: "Configuring GitHub on Windows"
date: 2011-12-15 23:21:44 -0700
comments: true
categories:  GitHub Windows
---

If you're a Windows developer like me, you've probably had trouble working with GitHub on Windows. Here's what I did. Hopefully this will be helpful to you.

Getting Started
---------------

First you need to grab a copy of [Git for Windows](http://code.google.com/p/msysgit/).
Once you've downloaded the latest version, launch it and go through the license and location screens.
Once you get to the "Select Components" screen, make sure both Context menu entries are selected.
If you want, select the TrueType font option.
That one shouldn't really matter.

<img class="size-full wp-image-1130 aligncenter" title="select-components" alt="" src="//itsananderson.blob.core.windows.net/post-images/select-components.png" width="539" height="428" />

Jump ahead a few more frames and you'll get to the SSH executable option.
Choose "OpenSSH", unless you have a compelling reason to use Plink.

<img class="aligncenter size-full wp-image-1131" title="openssh" alt="" src="//itsananderson.blob.core.windows.net/post-images/openssh.png" width="546" height="432" />

Run through the rest of the frames, accepting the defaults.
You now have Git installed on your machine, but we're not quite done yet.
GitHub uses public/private key encryption.
This means you don't commit with a username and password, like most SVN setups.
Instead, you tell GitHub your public key, then use your private key to authenticate.

Generating a Public Key
-----------------------

GitHub has a detailed article on setting up your SSH keys on Mac/Windows/Linux.
I recommend [checking it out](https://help.github.com/articles/generating-ssh-keys).

Clone a Repository From GitHub
------------------------------

You're just about there.
Go to your GitHub repository and select and copy the text in the textbox near the top of the page.
Make sure SSH is selected.

<img class="aligncenter size-full wp-image-1135" title="github-url" alt="" src="//itsananderson.blob.core.windows.net/post-images/github-url.png" width="664" height="33" />

Open up Git Bash.
You should be able to find it in your start menu.
"cd" to the directory where you plan on checking out your code.
For example, if you want your code to be located in C:\programming\project then you want to "cd" to C:\programming.

Type out "git clone ", but don't hit enter yet.
Go to the top left corner of the Git Bash window and click.
In the menu that pops up, select Edit -> Paste to paste the URL of your repository, but still don't hit enter.
Finally, type the name you want to call the project folder.
In our example, you'd want something like this:

`git clone git@github.com:itsananderson/wp-sendgrid.git project`

Now you can hit enter :)

If you entered a passphrase for your private key, it will ask you for it.
If all goes well, your repository should be downloaded.

Some Sample Commands
--------------------

This isn't really a tutorial on Git commands, but here are a few to get you started:

```bash
# See what files have changed:
git status

# Add a file (new or modified, it doesn't matter. You need to add it to commit it)
git add file.php

# Add several files:
git add *.php

# Commit your changes locally (just stores a local snapshot of your changes):  
git commit -m "Your commit message here"

# Push your changes up to GitHub:
git push -u origin master

# Pull down updates from GitHub:
git pull
```

That's about it.
There's more to Git (which I'm still learning about), but this should hopefully be enough to help you get started.