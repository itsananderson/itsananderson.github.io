---
layout: post
title: "Creating XAMPP Alias Directories"
date: 2011-02-14 13:35:42 -0700
comments: true
categories: Apache, XAMPP
---

If you've read my post on [adding aliases to WampServer](/blog/2009/01/14/wamp-alias-directories/),
you may be curious about how to add aliases to XAMPP.
While WampServer has a nice (if somewhat buggy) tool for adding Apache aliases, XAMPP does not.
Fortunately, creating XAMPP alias directories by hand is pretty easy.

Preparing to Create XAMPP Alias Directories
---

The first thing you'll want to do is add an alias directory to your XAMPP install.
If you're running Windows, create the following folder.

```
C:\xampp\apache\conf\alias
```

Next, you'll need to change your Apache configuration file.
You can find it under `C:\xampp\apache\conf\httpd.conf`.
To make changes, you'll need to edit it as an administrator.
If you're running Windows Vista or above, your best bet is to open Notepad as an administrator and then open httpd.conf.
To open Notepad (or any other application) as an administrator, right-click on it and select "Run as administrator".
You can also launch Notepad as an administrator from the start menu with `Ctrl+Shift+Enter` while Notepad is selected.

Once you've opened httpd.conf, add the following to the end and save it.

```
Include "conf/alias/*"
```

Now Apache will look in the alias folder for more configuration files.
This way you can add an alias by simply adding a new configuration file to the conf/alias/ folder.

Creating XAMPP Alias Directories
---

Let's add some XAMPP alias directories.
Suppose you want to add an alias called "dev".
First, download [this alias template file](/uploads/xampp-alias-template.txt) and place it in the alias folder.
Rename it to "dev.conf" (or whatever you called your alias).
You'll need to edit the template (again, as an administrator) and replace "DIRECTORY" (it's there twice) with the path to the alias and "ALIAS" with the name of your alias.
In our example, it looks something like this:

```
<directory "c:\users\foo\programming\dev">
    #
    # Possible values for the Options directive are "None", "All",
    # or any combination of:
    #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
    #
    # Note that "MultiViews" must be named *explicitly* --- "Options All"
    # doesn't give it to you.
    #
    # The Options directive is both complicated and important.  Please see
    # http://httpd.apache.org/docs/2.2/mod/core.html#options
    # for more information.
    #
    Options Indexes FollowSymLinks Includes ExecCGI

    #
    # AllowOverride controls what directives may be placed in .htaccess files.
    # It can be "All", "None", or any combination of the keywords:
    #   Options FileInfo AuthConfig Limit
    #
    AllowOverride All

    #
    # Controls who can get stuff from this server.
    #     Order allow,deny
    Allow from all
</Directory>

Alias /dev "C:\users\foo\programming\dev"
```

Restart Apache (you can do so from the XAMPP control panel) and check out http://localhost/dev.
You can repeat this process with other aliases to add more XAMPP alias directories.