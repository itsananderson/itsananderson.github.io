---
layout: post
title: "Even Smarter Post Formats"
date: 2010-12-13 21:54:19 -0700
comments: true
categories: WordPress
---

[Dougal Campbell](http://dougal.gunters.org/) published [an article](http://dougal.gunters.org/blog/2010/12/10/smarter-post-formats) the other day suggesting that the way many themes are handling the new Post Formats feature is wrong. While I completely agree that Post Formats shouldn't be handled with giant if/else blocks, I'm not sure the proposed solution is the best option either. Here's why.

What Dougal suggests is that theme developers instead dynamically include another template file to handle the post format. I've seen a similar idea suggested in a [Trac ticket](http://core.trac.wordpress.org/ticket/15385), and the issue I saw (but neglected to comment on) there is the same one I see here. Including PHP files is a relatively expensive operation.

Now, admittedly, including files isn't the worst thing you can do, and WordPress already includes dozens of files for every page request. Still, that doesn't mean you shouldn't try to get as much performance out of your theme as possible. On an average page load on an average website, using the `get_template_part()` method for Post Formats will cause 10 additional includes, but what happens if the blog admin decides they want to display 50 posts per page? The solution doesn't scale very well.

The alternate solution I propose is to dynamically call functions via [call_user_func](http://www.php.net/manual/en/function.call-user-func.php). When the name of a function is passed to `call_user_func`, that function is invoked. Incidentally, that's actually how WordPress does function callbacks for its actions and filters.

Here's how the code might look.

First, in your functions.php file, define functions for the Post Formats you wish to support. You could also place them in a separate file (like formats.php for example) and include that from your functions.php. That way you can keep your logic separated from your display, while only adding one additional include instead of ten.

```php
function format_aside() {
    // Code to display an aside post
}

function format_gallery() {
    // Code to display a gallery post
}

function format_standard() {
    // Code to display a standard post
}
```

Next, in your template, use the post format to determine which function to call. To make your theme more robust, make sure the post format is supported before you try to call the function. If it isn't, call your backup "default" format function.

```
while ( the_loop() ):
    $format = get_post_format();
    if ( function_exists( 'format_' . $format ) ) {
        call_user_func( 'format_' . $format );
    } else {
        call_user_func( 'format_standard' );
    }
endwhile;
```