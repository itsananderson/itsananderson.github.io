---
layout: post
title: "Creating a Custom Chrome Search Engine"
date: 2011-11-29 12:50:03 -0700
comments: true
categories: Rose-Hulman, Chrome
---

Custom search engines in Chrome are a pretty cool feature.
For example, [Jimmy Theis has a great blog post](http://jetheis.com/blog/2011/11/28/accelerating-rose-hulman-schedule-lookup-with-google-chrome/)
about using them to look up someone's Rose-Hulman class schedule. The one shortcoming I've found is that you can't do any processing of the actual search term.
In other words, you have to blindly jump to the search engine URL without evaluating the actual query.

In the case of the Rose-Hulman lookup page, there are actually two parameters that it would be nice to query on.
The first is the username of the person you're looking up, and the second is which quarter (Fall, Winter, Spring, Summer) to show.
With Chrome's custom search engines, you can't specify multiple parameters, so you have to hardcode the quarter parameter (which is hardly convenient).
To resolve this problem, I wrote a simple PHP script that takes in the search a search query (like `anderswc 1` or `jetheis 201130`) and translates it to the correct schedule lookup page query (my fall schedule, or Jimmy's spring schedule last year).
Here's the script:

```php
	<?php

	function get_academic_year() {
		if ( intval( date('n') ) >= 8 ) {
			return date( 'Y' ) + 1;
		} else {
			return intval( date( 'Y' ) );
		}
	}

	function get_quarter() {
		$month = intval( date( 'n' ) );
		$day = intval( date( 'j' ) );

		if ( 6 == $month || 7 == $month ) {
			return 40;
		} elseif ( ( 8 <= $month && 10 >= $month ) || ( 11 == $month && 20 >= $day ) ) {
			return 10;
		} elseif ( 11 <= $month || 2 >= $month ) {
			return 20;
		} else {
			return 30;
		}
	}

	$search = $_GET['search'];

	$parts = explode( ' ', $search );

	if ( 1 == count( $parts ) ) {
		$term = get_academic_year() . get_quarter();
	} else {
		$term = $parts[1];
		if ( strlen( $term ) == 1 ) {
			$term = get_academic_year() . $term . '0';
		} elseif ( strlen( $term ) == 2 ) {
			$term = get_academic_year() . $term;
		}
		$term = intval( $term );
	}
	$url = 'https://prodweb.rose-hulman.edu/regweb-cgi/reg-sched.pl?termcode=' . $term . '&view=tgrid&id1=' . $parts[0] . '&bt1=ID%2Username';

	header( 'Location: ' . $url );
```

Now you can create a custom search engine to point to this script (I've got a copy running at http://itsananderson.com/schedule.php), and specify "?search=%s".
For example, this is the custom search engine definition I have:

`http://itsananderson.com/schedule.php?search=%s`

Search syntax is pretty simple.

* "&lt;username>" to look up a user's current schedule.
* "&lt;username> &lt;quarter>" to look up a user's schedule for a specific quarter this year. Specify quarters as either 1, 2, 3, 4 or 10, 20, 30, 40
* "&lt;username> &lt;year>&lt;quarter>" to look up a user's schedule for a specific quarter and academic year. Quarters must be formatted with a trailing zero. Example: 201220 is Winter quarter, 2011-2012