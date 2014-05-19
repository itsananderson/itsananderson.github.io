---
layout: post
title: "Limit Your WordPress RSS Feed To One Category"
date: 2009-03-17 22:23:38 -0700
comments: true
categories: WordPress
---

When designing a recent website, I decided to use Posts for both news items and Portfolio entries. There were several advantages to doing this, but one issue I found was that everything was being thrown together in the RSS feed. I decided to look into filtering the RSS feed to only show posts from the News category. Here's how it works.

As with just about anything in WordPress, you can use Actions and Filters to customize the way WordPress RSS Feeds. I chose to filter the query because it already has built in category filtering functionality. First I hooked into the query filter.

```php
add_filter('pre_get_posts', 'filterRSSQuery');
```

Then I created a function called 'filterRSSQuery' which does just what the name suggests.

```php
function filterRSSQuery($query) {
	if ( $query->is_feed ) {
		$query->set('category_name', 'News');
	}
	return $query;
}
```

The code is pretty straight foreward, but here's a breakdown. First we test to see if the query is for a feed (we don't want to do this filtering anywhere else). Then we set the 'category_name' argument to 'News' (you would change this to whatever category you want to show up). Finally, you return the modified query object.

Overall, this is pretty simple, but extremely powerful.