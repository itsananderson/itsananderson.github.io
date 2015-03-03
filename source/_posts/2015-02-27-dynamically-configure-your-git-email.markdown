---
layout: post
title: "Dynamically Configure Your Git Email"
date: 2015-02-27 14:29:42 -0800
comments: true
categories: Git
---

> This post is the second in a series on tuning your Git environment.
> The first post on [customizing your git log](http://willi.am/blog/2015/02/19/customize-your-git-log-format/) isn't a prerequisite, but may be useful.

Managing Multiple Commit Emails
---

If you use Git at work and in your personal time, managing the email you use for commit messages can be tricky.

When my previous team first started using Git, I had several instances where I accidentally committed to our team repository with my personal email address.
This wasn't a big problem, but it made the repository history look a little messy, and it meant my picture didn't show up properly in the UI tools.

Simple Fix: Manually Configure Email Per Repository
---

My first solution was to remove the global configuration for my email address:

`git config --global --unset user.email`

This way when I committed into a fresh repository, I'd get a warning that I hadn't configured my email.

```
Your name and email address were configured automatically based
on your username and hostname. Please check that they are accurate.
You can suppress this message by setting them explicitly:

    git config --global user.name "Your Name"
    git config --global user.email you@example.com

After doing this, you may fix the identity used for this commit with:

    git commit --amend --reset-author

 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 foo1
```

Then I just had to configure my email **locally** for that repository:

`git config user.email will@itsananderson.com`

And update the commit with the right author data:

`git commit --amend --reset-author`

Medium Fix: Add Some Git Aliases
---

Running through those steps for every new repository quickly becomes tedious, especially when you like to spin up GitHub projects for every idea that pops into your head.

To simplify things, I first created an alias for the `git commit --amend --reset-author` command.
I called it `cara` for "Commit Amend Reset Author", but you could call it whatever you'll remember.
With that alias, it's super quick to fix a commit if I create it with the wrong author email.

The next thing I aliased was a script to guess the author email based on the repository URL.
I created a script and put it in `~/.git-scripts/email-guess.sh` (again, call it whatever you'll remember).

```sh
#!/bin/bash

remote=`git remote -v | awk '/\(push\)$/ {print $2}'`
email=will@itsananderson.com # default

if [[ $remote == *github.com:Microsoft* ]]; then
  email=wiand@microsoft.com
fi
if [[ $remote == *itsananderson.visualstudio.com* ]]; then
  email=will@codeawhile.com
fi

echo "Configuring user.email as $email"
git config user.email $email
```

The script isn't exactly a masterpiece, but it gets the job done.
[Feel free to copy and modify it to meet your needs](https://github.com/itsananderson/git-better/blob/master/itsananderson/git-scripts/email-guess.sh).

For the script to be useful, I aliased it as `git email-guess`:

`git config --global alias.email-guess \!". ~/.git-scripts/email-guess.sh" ""`

So now when I clone a new repository, I just `git email-guess` to set the email.
If I forgot to do it before my first commit, I follow it with `git cara` to fix that commit's author.

Advanced Fix: Full Automation
---

Reducing the work of author email management to only 1 or 2 commands is nice, but I wanted to eliminate it altogether.
This is where [Git hooks](http://www.git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) came in handy.

You can configure a Git hook manually for each repository, but again, we want this to be automated.
To do that, you'll want to modify the template hooks Git uses when it creates new repositories.

On Windows, these hook templates are located somewhere near `C:\Program Files (x86)\Git\share\git-core\templates\hooks`.
On OS X and Linux, they should be somewhere under the Git directory.
Running `which git` or <code>cat &#96;which git&#96;</code> should point you in the right direction.

Once you locate the template hooks directory, create a file called `post-checkout` with the following contents:

```sh
#!/bin/bash

if [[ $1 == 00000000000* ]]; then
  git email-guess
fi
```

All this does is test whether Git checked out a freshly cloned repository (thus "previous SHA" is all zeros).
If so, it runs `git email-guess` to set up the correct author email.

Wrapping Up
---

So that's the full rundown of my email configuration setup.
As I said in the previous post, you should take this as an example that you can customize it to fit your needs.

You can find most of my Git configuration in my [git-better](https://github.com/itsananderson/git-better) repository on GitHub.

As one example, there are many other Git hooks that you can take advantage of.
[Take a look](http://www.git-scm.com/book/en/v2/Customizing-Git-Git-Hooks) to see if there are any that you could use to automate a frequent task.
