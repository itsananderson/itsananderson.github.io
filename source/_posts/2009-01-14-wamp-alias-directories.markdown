---
layout: post
title: "Wamp Alias Directories"
date: 2009-01-14 13:45:43 -0700
comments: true
categories: Apache Wamp
---

I use <a href="http://www.wampserver.com/">Wamp Server</a> together with <a href="http://www.jetbrains.com/phpstorm/">PhpStorm</a> to develop PHP applications on my local machine.
This works well because I can do debugging (thanks to <a href="http://www.xdebug.org/">xdebug</a>) and can readily view and edit files.
I usually don't like to place projects in the <code>c:\wamp\www</code> folder, preferring instead to place them in more logical places in my personal directory.
Wamp alias directories are a great way to improve project organization.

The easiest way to facilitate this is to create alias directories in Apache that point to these different locations. Wamp Server provides an interface for creating wamp alias directories for Apache.

Adding Wamp Alias Directories
--------------

Click on the tray icon and select Apache &rarr; Alias directories &rarr;  Add an alias. A new command prompt window opens where you can enter the alias information.

Creating an alias like this, you'll probably notice that in order for the URL to work, you have to have a trailing slash at the end (e.g. `http://localhost/example/` will work while `http://localhost/example` won't). This can be a bit of a pain, but there is fortunately a quick fix.

Fixing Your Wamp Alias Directories
-------

Click on the Wamp Server tray icon again and select Apache &rarr; Alias directories &rarr; [alias url] -> Edit alias. This will open the alias file in Notepad. Remove the trailing slash from the relative URL.

For Example

`Alias /example/ "c:/path/to/example/"`

Would become

`Alias /example "c:/path/to/example/"`

Save the file and close Notepad. Wamp Server should restart automatically (if not, you can restart it manually from the tray icon). Once it has restarted, you're alias should now work with or without the trailing slash. Why Wamp Server doesn't do this automatically, I'm not sure, but at least there's a simple fix.

Updating Wamp so Alias Directories Just Work
-----

Thanks to Ozan Hazer for pointing out that you can actually change the alias template, and save yourself from having to perform multiple steps every time you add an alias.

Open `c:\wamp\scripts\addAlias.php` and locate the line starting with `$newConfFileContents = `.

```
# Find:
$newConfFileContents = 'Alias /'.$newAliasDir.'/

# Replace it with:
$newConfFileContents = 'Alias /'.$newAliasDir.'
```

Open `c:\wamp\scripts\refresh.php`.

```
# Find:
preg_match('|^Alias /'.$newalias_dir.'/ "(.+)"|',$alias_contents,$match);

# Replace it with:
preg_match('|^Alias /'.$newalias_dir.'/ "(.+)"|',$alias_contents,$match);
```

Be sure to back up these files before you edit them, since breaking them could potentially break other Wamp functionality.