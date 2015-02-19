---
layout: post
title: "Customize Your Git Log Format"
date: 2015-02-19 14:29:06 -0800
comments: true
categories: Git
---

{% blockquote %}
Over the last few months, I've been learning about woodworking in my spare time. As I've read articles and watched YouTube videos, I've noticed that the most successful woodworkers have a detailed knowledge of their tools. Not only do they know how to use and maintain the tools of their craft, but they're not afraid to modify them to better serve their needs. When they need a custom tool or jig to speed up their work, they make one. Watching and reading about these craftsmen and their tools, I've noticed parallels in my full time craft: Software Development.

In the next few posts, I'll share some of the software "jigs" I've created to make myself more efficient. The primary focus posts will be configuration and customization of Git.
{% endblockquote %}

Git Log: Your Own Personal Journal
---

If you're not already aware, your Git log is a critical tool for working with source control.
Because your memory's not perfect, a good log helps you recall what you've done, and the motivation behind it.

I've [written before](http://willi.am/blog/2014/05/06/git-for-devs-who-cant-commit-good/) about writing good commit messages, as has David in his series ["Committed to Good Commits"](http://www.davidruttka.com/blog/2014/06/04/committed-to-good-commits-messages/).
If your commit logs are sprinkled with gems like "did stuff" and "it finally works!", you'll want to check out those posts.

Choose Your Details With `--format`
---

Depending on what you're looking for in your log, you may want different levels of detail.
The Git log documentation has a [full list of the built-in formats](https://www.kernel.org/pub/software/scm/git/docs/git-log.html#_pretty_formats).

For example, if you're trying to find the SHA for a recent change, you may just want to see the SHA and the commit subject for each commit.
In that case, `git log --format=oneline` or `git log --oneline` is what you need.

{% img http://itsananderson.blob.core.windows.net/post-images/git-log-oneline.png %}

If you need verbose output, including the full message and author info, `--format=medium` is a good option.

{% img http://itsananderson.blob.core.windows.net/post-images/git-log-medium.png %}

Finally, if you want every single detail from the commit, `--format=fuller` will give you that.

{% img http://itsananderson.blob.core.windows.net/post-images/git-log-fuller.png %}

If you want to make any of these formats your default, you can do so with the following command:

```bash
git config --global format.pretty oneline # replace oneline with the format you want
```

Limit Results With `-n`
---

If you only want to see the last few commits, you can use the `-n` flag.
The more verbose version is `-n <number>` (e.g. `-n 5` to show the last 5 commits).

Alternatively, you can use an even quicker shortcut.
Just use `-<number>`. For example: `git log -5` will show the last 5 commits.

Fully Customize Your Log Output
---

Depending on how you use your commit log, you may find that no built-in log format exactly fits your needs.
What I've found is that the verbose formats are too hard to scan through, and the succinct ones don't have vital info.

To solve this problem, I created a custom log format, which I use almost exclusively.

You can configure my format with the following command:

{% codeblock %}
git config --global format.pretty format:"%C(auto)%h %d%Creset %s%n%Cgreen%ad%Creset %aN <%aE>%n"
{% endcodeblock %}

The output looks like this:

{% img http://itsananderson.blob.core.windows.net/post-images/git-log-custom.png %}

This format also works with the `--graph` flag:

{% img http://itsananderson.blob.core.windows.net/post-images/git-log-graph-format.png %}

What I like about this format is that it only uses three lines per commit, but it has all the information I care about.
I also inserted color codes for the various parts of the message, which makes it even easier to grok.

In Conclusion
---

Hopefully these examples give you some ideas about what you can do to customize your commit log.
Don't feel like you need to use my custom log format as-is.
The whole point is to customize it to fit your needs.

If you want to test out different custom formats quickly, you can pass them directly to `git log` like so:

{% codeblock %}
git log --format=format:"%C(auto)%h %C(green)%aN%Creset %s"
{% endcodeblock %}

{% img http://itsananderson.blob.core.windows.net/post-images/git-log-custom-flag.png %}

You can find the different placeholder options in the same [Pretty Formats](https://www.kernel.org/pub/software/scm/git/docs/git-log.html#_pretty_formats) section of the Git Log documentation.
