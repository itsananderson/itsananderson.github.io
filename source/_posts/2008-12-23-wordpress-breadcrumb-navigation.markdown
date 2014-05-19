---
layout: post
title: "WordPress Breadcrumb Navigation"
date: 2008-12-23 22:54:39 -0700
comments: true
categories: WordPress
---

[Bread Crumbs](http://en.wikipedia.org/wiki/Breadcrumb_(navigation)") are something that's been around for quite some time.
I first remember seeing them in <a title="ANGEL Learning" href="http://angellearning.com/">ANGEL</a> when my dad was working for the company in 2005. Similar things can be found in File Browsers, and browser history is really a kind of bread crumb.

In my work for IRPA one of the concerns we've had is making it easy to navigate the site. Because WordPress has limited CMS functionality out of the box, navigation is sometimes difficult on sites with many nested pages. One of the solutions I looked at was Breadcrumbs because they can give users a quick look at their location in the site structure. Over the break (which I'm still enjoying) I've been able to finish implementing breadcrumbs on the IRPA Site.

I almost think something like this should be in core, but again, WordPress isn't a CMS, so maybe it wouldn't be useful for most people.

One of the things that took me the longest was figuring out how to handle different site settings. A site who's home page displays latest posts needs a slightly different navigation list than a site which has a static home page. Even sites with static pages can have different structures depending on what page is the "home" page. Anyway, Here's the code if you want to add it to your template. Simply paste the code into your `functions.php` file and add a call to `get_breadcrumbs()` where you want the breadcrumb list in your template. It outputs the breadcrumbs as list items. You'll need to put the `ul` around them yourself in the template.

```php
function get_breadcrumbs(){
	global $post;

	$separator = '  &gt; '; // what to place between the pages

	if ( is_page() ){
		// bread crumb structure only logical on pages
		$trail = array($post); // initially $trail only contains the current page
		$parent = $post; // initially set to current post
		$show_on_front = get_option( 'show_on_front'); // does the front page display the latest posts or a static page
		$page_on_front = get_option( 'page_on_front' ); // if it shows a page, what page
		// while the current page isn't the home page and it has a parent
		while ( $parent->post_parent && !($parent->ID == $page_on_front && 'page') == $show_on_front ){
			$parent = get_post( $parent->post_parent ); // get the current page's parent
			array_unshift( $trail, $parent ); // add the parent object to beginning of array
		}
		if ( 'posts' == $show_on_front ) // if the front page shows latest posts, simply display a home link
			echo "<li class='breadcrumb-item' id='breadcrumb-0'><a href='" . get_bloginfo('home') . "'>Home</a></li>\n"; // home page link
		else{ // if the front page displays a static page, display a link to it
			$home_page = get_post( $page_on_front ); // get the front page object
			echo "<li class='breadcrumb-item' id='breadcrumb-{$home_page->ID}'><a href='" . get_bloginfo('home') . "'>$home_page->post_title</a></li>\n"; // home page link
			if($trail[0]->ID == $page_on_front) // if the home page is an ancestor of this page
				array_shift( $trail ); // remove the home page from the $trail because we've already printed it
		}
		foreach ( $trail as $page){
			// print the link to the current page in the foreach
			echo "<li class='breadcrumb-item' id='breadcrumb-{$page->ID}' >$separator<a href='" . get_page_link( $page->ID ) . "'>{$page->post_title}</a></li>\n";
		}
	}else{
		// if what we're looking at isn't a page, simply display a home link
		echo "<li class='breadcrumb-item' id='breadcrumb-0'><a href='" . get_bloginfo('home') . "'>Home</a></li>\n"; // home page link
	}
}
```

If you have any corrections or suggestions for this (rather long) bit of code, please add your comment bellow. I love feedback!