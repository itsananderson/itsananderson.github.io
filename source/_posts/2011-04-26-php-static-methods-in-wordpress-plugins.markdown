---
layout: post
title: "PHP Static Methods in WordPress Plugins"
date: 2011-04-26 13:16:52 -0700
comments: true
categories: WordPress
---

<p style="background-color: #faa; padding: 5px">
**Update 5/18/2014 This is a 3 year old post, and I've learned a lot in that time. Using this method to namespace your plugins makes it difficult to mock dependencies for unit tests. I leave this post up for posterity, but I no longer recommend this design pattern.**<p>

This evening I was chatting with Nick Ohrn and I threw out an idea that I'd been thinking about for a while. What if WordPress plugin developers used PHP static methods to namespace their plugins?

An Example Without PHP Static Methods
---

To understand what I'm babbling on about, consider a very basic plugin that adds square brackets around post titles.

```php
/*
 * Plugin Name: Add Brackets
 */
function ab_add_brackets( $title ) {
    return "[$title]";
}
add_filter( 'the_title', 'ab_add_brackets' );
```

It really doesn't get much simpler than that, but the problem is that there might be another plugin that uses the same prefix. Also, if you decide to change the prefix, you'll have to go through and change all of your hook and action registrations too. What a pain.

Class Namespaces, but no PHP Static Methods
---

The next option is to use a class as a namespacing tool. This is fairly common among more experienced WordPress developers.

```php
/*
 * Plugin Name: Add Brackets
 */
if ( !class_exists('Add_Brackets') ) {
    class Add_Brackets {
        private static $instance;
        private function __construct() {
            add_filter( 'the_title', array( $this, 'add_brackets' ) );
        }
        public static function get_instance() {
            if ( null == self::$instance ) {
                self::$instance = new Add_Brackets();
            }
            return self::$instance;
        }
        public function add_brackets( $title ) {
            return "[$title]";
        }
    }
    Add_Brackets::get_instance();
}
```

Utilizing PHP Static Methods
---

What I propose is developers use static methods instead. There's very little difference between a singleton class with instance methods and a class with static methods, and simply using static methods can simplify your code. Check out this example.

```php
/*
 * Plugin Name: Add Brackets
 */
if ( !class_exists('Add_Brackets') ) {
    class Add_Brackets {
        public static function start() {
            add_filter( 'the_title', array( __CLASS__, 'add_brackets' ) );
        }
        public static function add_brackets( $title ) {
            return "[$title]";
        }
    }
    Add_Brackets::start();
}
```

So that's that. All the advantages of namespacing with classes, with a small reduction in memory overhead (because you're passing around strings, rather than objects). Static methods are a PHP5 feature, but with PHP4 support being phased out in WordPress 3.2, this seems like a reasonable next step for WordPress developers to take.

What do you think? Are there any issues with this technique that I've missed? Are there any improvements that could be made? Let me know in the comments!