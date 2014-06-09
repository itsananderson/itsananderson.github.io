---
layout: page
title: "Code Snippets"
date: 2014-05-18 11:44
comments: true
sharing: true
footer: true
---

Code Snippets allows you to replace arbitrary text with other arbitrary text.

What it does:

* Arbitrary Snippet "Keys" - You don't have to surround them with brackets. They can literally be *whatever* you want
* Recursive Expansion - If you include another Snippet 'Key' in the text of another Snippet, it can be expanded recursively.
* Circular Snippet Checking - Keeps you from creating an infinite expansion loop by Snippets that reference each other.
* Case Insensitivity - You can chose to replace Snippet 'Keys' without regard to case.

What it doesn't do:

* No Attributes - Code Snippets doesn't (currently) support attributes (for example `[img src="/images/cat.png"]`. Snippets are static, meaning they can't be changed using arguments.

Installation
------------

As with all WordPress plugins, you'll need to [download](http://www.itsananderson.com/download/code-snippets.1.1.zip) the archive and upload its contents into your WordPress plugin directory.
Once it has been uploaded you'll need to activate it from the administration menu.

After Code Snippets has been activated, a new submenu page will appear in Settings called 'Code Snippets'. This is where you can create and edit Code Snippets.

Usage
-----

Empty input boxes will always appear below the list of current Snippets.
Enter the Snippet information and click 'Save Snippets' to add a new Snippet.

To delete a snippet, delete the text in its name field and click 'Save Snippets'.

Be careful not to create two snippets with the same name. If you do this, the first duplicate snippet will, unfortunately, be lost.

If you want a snippet to replace 'keys' without regard for case, uncheck 'Case Sensitive'.

If you don't want to expand snippet keys a snippet's value may contain (see the third example), uncheck 'Expand'.

Examples
--------

1. I'll start with the reason I created Code Snippets. I wanted a super simple way to add Google Syntax Highlighting by way of the <a href="http://wordpress.org/extend/plugins/google-syntax-highlighter/">WordPress Plugin</a> of the same name. Doing this required remembering and typing out a somewhat long piece of code. By using Code Snippets, I can now simply type out `[php]` and have it replaced with `<pre name="code" class="php">`.
1. A simpler example is simply word replacement. Let's say you want to replace all occurrences of 'WordPress' with 'Habari' (you heretic!). This is simple with Code Snippets. Simply define a snippet who's name is <code>WordPress</code> and who's value is <code>Habari</code>. If you want to replace ALL occurrences of WordPress without regard for case, simply uncheck "Case Sensitive".
1. Let's assume we've already added the previous example, but now we want to take all occurrences of 'Habari' with a link to the Habari project. Simply create another Code Snippet who's name is `Habari` and who's value is `<a href="http://www.habariproject.org/" >Habari</a>`. Here you *must* uncheck 'Expand' since you're including the Snippet name in its value (failure to uncheck 'Expand' would cause a Circular Snippet Error). You can also uncheck 'Case Sensitive' if you'd like to replace *all* occurrences of 'Habari' regardless of case.