---
layout: post
title: "Git for Devs Who Can't Commit Good"
date: 2014-05-06 13:50:34 -0700
comments: true
categories: Git
---

Two big Git mantras are "Commit early, commit often" and "Branches are cheap".

When I started using Git, I had these ideas in the back of my mind.
Unfortunately, I only really thought of them after I'd done a huge chunk of work without committing any code.
I'd type `git status` or `git diff` and scroll through my changes, but I'd eventually admit defeat and commit everything.
I'd promise to commit more frequently "next time", but it never seemed to happen.

Frequent committing is something that you learn as a way to avoid pain.
In a typical chunk of dev time, it's not uncommon to make a sweeping change, and then realize that the change was a bad decision.
Without commit history, the only way to get back the previous version is to rely on your editor's undo function.
This causes problems when you have a mix of changes that you want to keep and changes that you want to undo.
Frequent, small commits can make it easier to jump pack to specific points.

{% img center /images/post-images/commit-for-ants.jpg %}

Creating Good Commits
---------------------

In addition to committing frequently, it's important to make good commits.
There are several important features aspects in a good commit.

### Good Commit Messages

Writing a good commit message takes more than avoiding "did stuff" messages.

A good commit message is a love note to your future self, or anyone else who works on your code.
A bad commit message is more of a suicide note.

A technique I picked up from [David](http://davidruttka.com/), one of my coworkers, is to do commit messages along the lines of the following:

    <short summary of changes in imperative voice>
    
    <longer paragraph describing the change>
    
    Fixes/Implements: #<issue or feature number>
    
    Before:
    <description of problem you are fixing or feature you are implementing>
    
    After:
    <how it behaves now that your change is applied>
    
    How I Tested It:
    <description of any unit tests, functional tests, or manual tests you ran to test this change>

This may seem verbose, but it really doesn't take long, and it gives you a huge advantage if you ever have to come back and figure out what this commit was all about.

This doesn't apply to every change.
If you're fixing a typo in a comment, or adding a missing semicolon to your JavaScript, you can probably get away with a one line commit message.

A good rule of thumb is that a competent dev should be able to look at your commit message and (approximately) duplicate your work without looking at the code.

Finally, your summary line should be written in imperative voice.
"Make changes" not "Made changes" or "Making changes".
This guarantees that your messages will mesh with Git's auto-generated messages for things like `revert` and `merge`.
It also makes it clear that you're talking about what this commit does, not what previous or future commits are doing.

For more thoughts on commit messages, see [A Note About Git Commit Messages](http://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html).

### The Right Files

It's tempting to use a `-a` flag to commit all pending changes, but in most cases you should put more thought into what files you include in a commit.

Consider an example.
Say you add a feature in file `a.js`, but in the process, you discover a typo in `b.js`.
It's tempting to commit both changes as one, because the typo fix is so small, but consider what happens if you later need to revert your commit.
Not only will you be reverting your feature, but you'll also be reverting the typo fix. This means you'll need to do a second commit to re-fix the typo change.
If you instead do two distinct commits, you can safely undo the problematic feature without touching the typo fix.

Of course, this doesn't mean you have to add each file individually.
There are many handy shortcuts for adding exactly the files you intend.

`git add file1 file2 file3` - Adds multiple files in one command

`git add folder/` - Adds all changes under a folder

`git add .` - Stages all modified and new files in the current folder

`git add -A .` - Stages all modifed and new files as well as staging any deleted files in the current folder

`git add '*.txt'` - Recursively adds all files ending in .txt

`git add *.txt` - Adds all files *in the current folder* ending in .txt

This approach doesn't just apply to different files.
What if you add a feature and fix a typo in the same file?  
Fortunately, `git add -p` makes it easy to [only stage parts of files](http://www.benhallbenhall.com/2013/01/git-add-p-patches-full-commits/).

If you "add" (or stage) a file that you don't want to commit, it's easy to remove.
Just run `git reset HEAD <file>`.

Using these techniques, you can create concise, cohesive, good looking commits.
Your future self will thank you.

{% img center /images/post-images/good-looking-commits.jpg %}

Learning to Do Other Stuff Good Too
-----------------------------------

There are a number of other techniques for creating a useful commit history.
Here are some that I've found especially useful.

**NOTE**: Only use these techniques if you haven't shared your changes.
If you've already pushed your changes, ammending your commit can cause problems for other collaborators on your project

### Fixing a Commit

One of the great features of Git is the ability to rewrite history.
The simplest way to do that is with the `git commit --amend` command.
This will take your current staged changes and apply them to the previous commit, "ammending" it.
You'll also get a chance to change the commit message.

This is useful for in a number of cases:

* You make a commit and forgot to add a file
* You missed adding a part of a file
* You had a typo in one of your changes
* You had a typo, or missing information in your commit message
 
### Squashing Commits

Sometimes you'll have silly commits like "forgot a file", and you don't need them to be distinct.
For the sake of your commit history, it can be good to flatten those commits.
This allows you to pretend that you never make any mistakes :)

Consider the following log

```
8fb97b5 Implement bla bla 

bb0ee77 Address feedback

bf582d6 Implement foo foo

...
```

If your changes from `Address feedback` are just cleanups to `Implement foo foo`, you probably don't need that extra commit sitting in your commit history.

I could walk through the process of rebasing, but there's already a pretty thorough tutorial on [squashing commits with rebase](http://gitready.com/advanced/2009/02/10/squashing-commits-with-rebase.html)
