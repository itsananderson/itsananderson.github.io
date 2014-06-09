---
layout: post
title: "WordPress Security Tip: Disable Folder Indexes"
date: 2008-12-30 22:49:40 -0700
comments: true
categories: Apache WordPress
---

I've been catching up on the WordPress Weekly Podcasts lately and while I was listening to one of the first shows I came across a nice security tip. Place empty index.php files in all directories that don't already have an index file. Directories like the /wp-content/plugins folder could be dangerous in the hands of hackers if one of the plugins you use has a security flaw. By obscuring the folder's contents you make it just a little harder for hackers to cause you problems.

I went about creating these empty index files on my site, but soon discovered that there are hosts of folders that could use them (think about the date structure in /wp-content/uploads ). Then I remembered another tool. `.htaccess`. Add this line of code to your `.htaccess` file to keep file in *any* directory from being listed (this works on any site who's server supports `.htaccess` by the way, not just WordPress blogs).

```
IndexIgnore *
```

Really simple, I know, but I thought I'd share it all the same.