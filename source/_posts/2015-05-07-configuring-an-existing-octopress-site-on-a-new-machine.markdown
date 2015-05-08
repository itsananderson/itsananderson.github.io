---
layout: post
title: "Configuring an Existing Octopress Site On a New Machine"
date: 2015-05-07 22:59:57 -0700
comments: true
categories: Octopress
---

Every time I set up a new machine, I have to figure out how write posts on my [Octopress](http://octopress.org) blog. I usually follow [these instructions](https://github.com/colca/colca.github.com/blob/source/source/_posts/2014-01-27-octopress-sync-octopress-on-different-machines.markdown), but have to tweak them slightly to work for me. I fanally decided to write down the exact steps I use so I don't have to figure them out every time. Hopefully it's useful for someone else as well.

### Step 1 - Run Octopress setup

```
git clone git://github.com/imathis/octopress.git octopress
cd octopress    # If you use RVM, You'll be asked if you trust the .rvmrc file (say yes).
ruby --version  # Should report Ruby 1.9.3
gem install bundler # On Linux/OSX you may need to run "sudo gem install bundler" instead
bundle install
rake install
rake setup_github_pages
```

The main purpose of setting up Octopress like this is to get the main layout configured correctly.

### Step 2 - Reset to your blog's branch tips

```
git fetch origin source
git branch --set-upstream-to=origin/source source
git reset --hard origin/source
cd _deploy
git fetch origin master
git branch --set-upstream-to=origin/master master
git reset --hard origin master
```
