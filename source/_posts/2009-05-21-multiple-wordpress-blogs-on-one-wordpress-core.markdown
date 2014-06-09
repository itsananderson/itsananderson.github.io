---
layout: post
title: "Multiple WordPress Blogs On One WordPress Core"
date: 2009-05-21 22:14:53 -0700
comments: true
categories: WordPress
---

Last Saturday I had the opportunity to listen to <a href="http://www.krotscheck.net/">Mike Krotscheck</a> talk about techniques for advanced WordPress installations. One of the most interesting things I took away from his talk was a way to install multiple WordPress blogs on one WordPress core. Note we're not talking about WordPress MU. When you only need 2 or 3 blogs, WordPress MU can be a bit overkill, but installing these blogs on the same WordPress core can save space and make updates easier. Here's how you do it.

First of course you need to download WordPress and then upload it to your server. This is the easy part, and there are lots of instructions for how to do this, so I won't go through them. You'll also need a MySQL database, but again I won't go into that since there are lots of resources already available that explain how to do this.

Let's suppose we're installing two blogs on domain1.com and domain2.com (lame I know, but give me a break). These should be pointing at the same server (your domain registrar can help you with this). If you navigate to your WordPress install, you'll be asked to go through the installation process. Let's start with domain1.com. Enter all your database information, but specify a custom table prefix (like "domain1_") which will be unique to that domain. Once the installation is complete (make sure you save your password!) you need to delete the wp-config.php file.

Now if you navigate with your other domain, you'll be asked to install WordPress again. This time, use a different table prefix (like "domain2_"). This will give you two different sets of WordPress tables in your database. The only problem is that you can't access both of them. We'll remedy this next.

Go into your wp-config.php file and find the line that looks something like this.

```php
$table_prefix  = 'domain2_';
```

We need this variable to be different, depending on what domain is being used to access your blog. Using the PHP $_SERVER super global, we can do this quite easily.

Here's the new code you'd want to use.

```php
if ( 'domain1.com' == $_SERVER['HTTP_HOST'] )  {
$table_prefix = 'domain1_';
} else {
$table_prefix = 'domain2_';
}
```

So now your server will display two different blogs, depending on what domain is used to access your site. Again, the advantage here is that you only use the space of one set of WordPress files, and you only have to do one update when a new version is released.

Mike talked about some other cool things you can do with this, and talked about how you can address issues with plugins like Google SiteMaps. He promised to post his slides, so if you'd like to learn more you should check out <a href="http://www.krotscheck.net/">his blog</a>.