---
layout: post
title: "WordPress Custom Post Statuses"
date: 2011-02-19 13:59:07 -0700
comments: true
categories: WordPress
---

WordPress displays post states on post list tables to indicate that a post has a special condition. It's possible to hook into that list of states and a WordPress custom post status. If you want to make certain posts stand out, or need a custom way to categorize your posts, this is simple way to do it.

The following screenshot illustrates some of the default post states.

![Before adding WordPress Custom Post Statuses](//itsananderson.blob.core.windows.net/post-images/wordpress-post-status-samples.png)

Adding a new WordPress Custom Post Status
---------

Adding your own states is actually pretty easy. The following code snippet should get you going in the right direction.

```php
class My_Custom_Post_State {
  function My_Custom_Post_State() {
    $this->__construct();
  }
  function __construct() {
    add_filter( 'display_post_states', array( $this, 'custom_post_state' ) );
  }
  function custom_post_state( $states ) {
    global $post;
    $show_custom_state = null !== get_post_meta( $post->ID, '_some_costom_field' );
    if ( $show_custom_state ) {
      $states[] = __( 'Custom State' );
    }
    return $states;
  }
}
new My_Custom_Post_State();
```

Result of Adding a WordPress Custom Post Status
---

Assuming you have code elsewhere that is adding the custom field to posts, you'll see something like this.

![After Adding WordPress Custom Post Statuses](//itsananderson.blob.core.windows.net/post-images/custom-state-sample.png)

If you added code to add a custom field called `_some_custom_field` to certain posts, those posts would show up with the 'Custom State' status. You could further customize this example to check the value of the custom meta field, and change the post status based on that field.

That's pretty much it. Obviously there are more useful examples, but this should be enough to get you started.