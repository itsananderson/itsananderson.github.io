---
layout: post
title: "Customizing the P2 WordPress Theme"
date: 2010-03-29 22:00:35 -0700
comments: true
categories: WordPress
---

As you may have seen, I launched a new blog about a week and a half ago to track how much sleep I'm getting. I think tracking my sleeping patterns has helped me get more rest, but that's not really what this post is about.

I thought it might be interesting to talk about some of the customizations I made to the <a href="http://wordpress.org/extend/themes/p2">P2 WordPress theme</a> that I'm using.

True to the WordPress paradigm, I didn't actually change anything in the P2 theme, but rather wrote a plugin to modify its behavior with filters and actions.

The most visible change I made was the addition of a "Sleep Counter" widget that displays the number of hours and minutes I've slept since March 18th. This actually relies on the the other parts of the plugin, though, so I'll leave the discussion of this widget until later.

The main purpose of this customization is to keep track of the hours I've slept programmatically. I could have done this with post meta values, but that would defeat the purpose of P2 because I'd have to go into the back end to create posts. Instead, I decided to simply put the information in the post and parse it using the `wp_insert_post` action.

This hook is called whenever a post is saved, right after it has been written to the database. The function is passed the ID of the post that has been created/edited. What I do is get the post content, parse it to find the number of hours/minutes recorded, and save those values to two post metas (or update the post metas if I'm editing the post). The function is as follows.

```php
function get_sleep_times($id) {
	$post = get_post($id);
	$content = $post->post_content;
	$matches = array();
	// pretty lazy regex matching here, but it works
	$matched = preg_match('/hours[^0123456789]*([0-9]*).*minutes[^0123456789]*([0-9]*)/i', $content, $matches);
	if ($matched < 1) // don't do anything if no sleep counts were posted.
		return;
	$hours = $matches[1];
	$minutes = $matches[2];
	update_post_meta($id, '_sleep_hours', $hours);
	update_post_meta($id, '_sleep_minutes', $minutes);
}
```

This code isn't perfect, but since I wrote it after about 24 hours without sleep and it is only intended to be used  by myself, I'll give myself a break.

The next step was to create the widget to display my total sleep count. I hadn't created any widgets since the new widget API was released in WordPress 2.8, but a great <a href="http://justintadlock.com/archives/2009/05/26/the-complete-guide-to-creating-widgets-in-wordpress-28">article by Justin Tadlock</a> set me on the right track. I added some customization options for how the sleep count is displayed, but the more interesting part is the function that actually displays the widget. It gets the options for the widget, then calls a `print_sleep_count` function that I defined. 


```php
function print_sleep_count($format = '') {
	global $wpdb;
	$ajax = empty($format);
	$format = $format ? $format : SleepCounterWidget::$default_format;
	
	$query = "SELECT COALESCE(SUM(meta_value), 0) as totalsum FROM {$wpdb->postmeta} " .
			"INNER JOIN {$wpdb->posts} ON {$wpdb->postmeta}.post_id = {$wpdb->posts}.ID " . 
			"WHERE {$wpdb->posts}.post_status <> 'trash' AND {$wpdb->postmeta}.meta_key = '%s'";
	
	$hours = intval($wpdb->get_var(sprintf($query, '_sleep_hours')));
	$minutes = intval($wpdb->get_var(sprintf($query, '_sleep_minutes')));
	
	printf($format, $hours + intval($minutes / 60), $minutes % 60);
	
	// exit if this is requested via AJAX
	// if we don't, WordPress will add '0' to the output
	!$ajax or die();
}
```

You'll notice some funky stuff with the `$ajax` variable. I'll explain that later. Just ignore it for now.

The main magic here is the the SQL query. I decided to use a SQL query instead of the post meta API because I wanted get an aggregate of the post metas, and doing the aggregation from within SQL is much more efficient. I built a customizable query so I could use it to get both the number of hours slept and the number of minutes slept. I use sprintf to insert the meta key I'm looking for when I send the query to `$wpdb`. The reason I decided against the $wpdb->prepare() function is efficiency. The result would have been the same, but that function does some argument escaping that isn't necessary because I know exactly what variables I'm passing in.

Once I get the total number of hours and minutes I've slept, I convert it to actual hours and minutes. This takes care of the situation where the sum of minutes is greater than 60 (one hour).

The final enhancement I made was some front end AJAX that updates the widget value whenever I create a new post. It's pretty basic, and just utilizes the <a href="http://codex.wordpress.org/AJAX_in_Plugins">Admin AJAX API</a> available within WordPress. The way I decided to do it was to utilize my existing `print_sleep_count` function. I simply hooked to an AJAX request event. That's where the `$ajax` variable I mentioned earlier comes in. If the format argument isn't passed to `print_sleep_count`, I assume it is being called via AJAX and exit at the end of the function.

You'll also notice that I've got a widget on my main website that displays the same sleep count. It is mostly identical to the one on my Sleep blog, but the database tables are hard coded (because here `$wpdb->tablename` uses the wrong prefix). I also decided to have the sleep count link to my Sleep blog.

And that's pretty much it. There are some enhancements I could probably make. For one thing, I could store the sleep count as one option in the options table to save myself from having to calculate an aggregate value every time, but with the number of visits I get each day, it's not enough of an issue to worry about.

If you'd like to see what the rest of the plugin looks like, you can <a href="http://itsananderson.com/download/sleep-counter.1.0.zip">download it</a> and check it out.
