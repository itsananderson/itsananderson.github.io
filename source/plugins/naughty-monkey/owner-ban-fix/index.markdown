---
layout: page
title: "Naughty Monkey Ban Fix"
date: 2014-05-18 00:09
comments: false
sharing: true
footer: true
---

Ok, now you've done it. You banned yourself from your own website. What a silly thing to do!

Here's the deal. you need to go into your the ban list and remove your IP address.
Sounds easy, right?
Yeah, that's what you said when I asked if you could avoid logging in as 'admin'.

Anyway, if you're not familiar with the WordPress structure, you can find the file you need to edit in `/wp-content/plugins/naughty-monkey/ban-list.php`

Ideally you should just remove your IP from that list, but if you mess it up and can't fix it, you can also replace the contents with the following code.

```php
<?php $ban_list = array() ?>
```

If you're still stuck, you can always send me an [email](mailto:will@itsananderson.com) and I can try to help, but keep in mind that I only do this for fun, so it could be a while before I can give you a hand.

<strong>Now just remember. Don't try to log in as 'admin'!</strong>