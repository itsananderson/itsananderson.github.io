---
layout: post
title: "PHP Code Completion for Globals"
date: 2011-01-28 20:57:07 -0700
comments: true
categories: PHP
---

Any IDE worth using provides code completion suggestions as you type. For example, this is what Netbeans suggests when you type "wp_" inside a WordPress project.

![](/images/post-images/netbeans-code-completion.png)

The problem with dynamically typed languages like PHP is that it can be difficult for the IDE to provide PHP code completion for variables that have been defined in the global scope. You can help your IDE by providing it with hints about the types of objects.

Concrete PHP Code Completion Example
------

The example I'll use is the $wpdb global variable defined in WordPress. It contains an instance of the "wpdb" class, which wraps around the database connection and provides a bunch of helper methods. It's defined in the global scope, so when you use it in another file, your IDE has no clue what type it is. This means it can't provide any PHP code completion.

If you add the following code snippet to the beginning of your function (or file, if applicable), it will tell your IDE the type of the $wpdb variable without actually affecting the execution of your code.

```php
if ( false ) {
    $wpdb = new wpdb();
}
```

As you can see, the code inside the if statement will never execute, but every IDE I've ever used isn't smart enough to figure that out, so they assume the type of the $wpdb variable is now wpdb.

When you add the snippet, you get PHP code completion suggestions that look something like this:

![](/images/post-images/netbeans-globals-code-completion.png)

The nice thing about doing this is that if you forget to remove the code snippet, it won't break anything. You'll lose a CPU cycle or two evaluating the if statement, but your code will run without any problems. It's probably a good idea to remove it before you release your code, though, since it might be confusing to anyone who looks at it later.

**Update**: You can also do this with PHPDoc.

```php
/**
 * @var $wpdb wpdb
 */
global $wpdb;
```

This also gives your IDE a hint about what type $wpdb has, and doesn't polute your code with a bunch of non-executing statements.