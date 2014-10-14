---
layout: post
title: "Unpacking a Git Database"
date: 2014-10-14 13:57:15 -0700
comments: true
categories: Git
---

While writing the [previous post](/blog/2014/10/14/for-the-last-time-git-stores-snapshots-not-diffs/), I discovered a cool Git command which I thought I'd share.

When you clone a Git repository, most Git hosts will send you a packed version of the database.
This is much more efficient than sending the raw Git objects, because duplicate content can be compressed.

However, if you want to inspect the object database with a file browser, it's hard to do so, because the content stays packed in a single file.
Unpacking is pretty simple.

If you unpack the `.pack` file while it's in the `.git/object/pack` folder, Git won't do anything, because it concludes that it already has all the objects contained in that `.pack`.
However, if you move the `.pack` file somewhere else, you can unpack it into your repository's database.

```
mv .git/object/pack/pack-*.pack .
cat pack-*.pack | git unpack-objects
rm pack-*.pack
```

And there you have it.
Your packed objects are unpacked into `.git/objects/` folder.

This isn't something I recommend doing as a general practice, since it can take up significantly more space, but it's a useful thing to be able to do if you want to manually inspect your Git database.
