---
layout: post
title: "Get Called Class Name in PHP"
date: 2011-09-05 13:09:43 -0700
comments: true
categories: 
---

I posted a few months ago about [using static methods in WordPress plugins](http://www.itsananderson.com/2011/04/using-static-methods-in-wordpress-plugins/).
I've used that method myself for the last few months, and I've really grown to love the style of code that this technique produces.
One part, however, was a source of some pain: Inheritance.
This can be solved by utilizing PHP's `get_called_class()` function to get called class names in from your static functions.

To make a long story short, getting the actual class name of the current object in an instance method is easy, just call `get_class( $this )`.
The problem is, $this doesn't exist in a static method, and just calling get_class() returns the current class (basically the same as __CLASS__), which isn't helpful if you actually want the name of a child class.

Why You Might Want to Get Called Class Names
--------------------------------------------

Consider a base class which has a `get_instance()` function, for creating an instance of the class, or returning an existing instance if it has already been instantiated.

```php
class Singleton {
    private static $instance;
    public static function get_instance() {
        if ( is_null( self::$instance ) ) {
            self::$instance = new Singleton();
        }
        return self::$instance;
    }
    private function __construct() {
        // Singleton constructor stuff
    }
}
```

How to Get Called Class Names and Improve Extensibility
-------------------------------------------------------

This is great, but the problem arises when we try to extend that singleton class.
Ideally, the inherited get_instance() method would return an instance of the actual class, but with the above implementation, it would always return an instance of the Singleton class. Here's an alternative, which uses the get_called_class() function to get called class names.

```php
class Singleton {
    private static $instances = array();
    public static function get_instance() {
        $class = get_called_class();
        if ( empty( self::$instances[$class] ) ) {
            self::$instances[$class] = new $class();
        }
        return self::$instance;
    }
    private function __construct() {
        // Singleton constructor stuff
    }
}

class Child extends Singleton {
    // Child class stuff
}
```

You'll notice that there are a few changes here. First, the Singleton class now stores instances in an associative array, keyed by class name. Second, it uses get_called_class() to get the class on which this method was invoked. This means calling Child::get_instance() will return an instance of Child, not Singleton. Pretty sweet, huh?