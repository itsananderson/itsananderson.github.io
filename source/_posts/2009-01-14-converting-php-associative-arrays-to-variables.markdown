---
layout: post
title: "Converting PHP Associative Arrays to Variables"
date: 2009-01-14 22:39:35 -0700
comments: true
categories: PHP
---

**EDIT: <a href="http://nickohrn.com/">Nick Ohrn</a> pointed out that <a href="http://us.php.net/extract" title="PHP Manual Entry for extract">extract</a> does this automatically. The following post is still a valid look at how this could be implemented if the functionality didn't already exist, but it is better to use extract because it is likely more efficient.**

Associative arrays are one of my favorite PHP features. They're simple to use and easy to understand. Sometimes though, being able to reference the values of associative arrays with variables can be more convenient. Today I'm going to share a simple way to convert a associative arrays to variables.

OK, if you're not sure what we're trying to accomplish here, take a look at the following example.

```php
$list = array( 'var1' =&gt; 'value1', 'var2' =&gt; 'value2' );
```

To reference the elements in the array, you'll have to use the array key like this.

```php
echo $list['var1']; //prints 'value1'
echo $list['var2']; //prints 'value2'
```

What we'd like to do instead is reference the values using variable names like this.

```php
echo $var1; //print 'value1'
echo $var2; //print 'value2'
```

This is possible through what the PHP Manual calls 'variable variables'. The idea is that the value of one variable is used as the name of another variable. Here's an example.

```php
$hello = 'world';
$$hello = 'howdy';
echo $world; //prints 'howdy'
```

Here, the value of `$hello` is analyzed and used as the name of the variable to which `'howdy'` is assigned.

Now it's not a big step to expand this to an associative array. Consider the following code.

```php
$list = array( 'var1' => 'value1', 'var2' => 'value2' );
foreach ( $list as $key => $value ) { $$key = $value; }
echo $var1; //prints 'value1'
echo $var2; //prints 'value2'
```

So we've accomplished what we set out to do. I must caution you, though, about a couple of things which might trip you up.

First, make sure the keys are valid variable names. If you don't you'll run into problems when you execute the script.

Second, be *very* careful when using this with user input. For example, the following code is insecure.

```php
// login.php
// checks the username and password in the GET request
$realuser = 'me';
$realpass = 'secret';
foreach ( $_GET as $key => $value ) { $$key = $value; }
if ( $user == $realuser && $pass == $realpass ) {
 echo 'login correct';
} else {
 echo 'login incorrect';
}
```

There are several problems here. First, we don't *know* that $user and $pass have been initialized. We should check this before using them. Second (and more importantly) a cleverly generated request could let a user log in without a correct username/password. For example: `login.php?user=me&pass=wrong&realpass=wrong`

When the script runs through the list of variables, it will assign a new value of `wrong` to $realpass. Because `$realpass` and `$pass` have the same value (as do `$user` and `$realuser`), the page will allow them to log in.

Here is a way to keep things a little safer by prefixing all the `$_GET[]` values.

```php
// login.php
// checks the username and password in GET request
$realuser = 'me';
$realpass = 'secret';
foreach ( $_GET as $key =&gt; $value ) { ${'get_'; . $key} = $value; }
if ( isset( $get_user ) &amp;&amp; isset( $get_pass )
    && $get_user == $realuser &amp;&amp; $get_pass == $realpass ) {
 echo 'login correct';
} else {
 echo 'login incorrect';
}
```

Now the `$_GET[]` values are prefixed with `get_` so they can't interfere with other variables in the script. Additionally `isset` is used to ensure that the values of the variables has actually been set.

To use something like this, you'd still need to check for invalid variable names, but I won't go into that for this example. 