---
layout: post
title: "The Obscure WordPress get_option Parameter"
date: 2009-05-04 22:18:54 -0700
comments: true
categories: WordPress
---

Any WordPress Plugin developer worth their salt knows about the `get_option` function, which allows options to be retrieved from the database. What I recently discovered is that this function accepts a second parameter, indicating a default value to be returned if the option doesn't exist in the database.

Here's a simple example of the "old" and "new" ways of retrieving options from the database:

old:

```php
$option = get_option( 'Option Name' );
if( false === $option ) {
$option = 'My real option value.';
}
```

new:

```php
$option = get_option('option_name', 'default');
```

As you can see, this simplifies option retrieval and makes your code just a little easier to read.

**EDIT:** Thanks [Nick Ohrn](http://nickohrn.com/) for pointing out a bug in the first code snippet. I've updated it so nobody is confused.
