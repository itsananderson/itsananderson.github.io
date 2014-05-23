---
layout: post
title: "Escaping Exclamation Marks in Bash"
date: 2014-05-22 18:25:22 -0700
comments: true
categories: Bash
---

If you've ever committed to Git in either frustration or excitement, you've probably seen a message like this:

```bash
$ git commit -m "It works!"
-bash: !": event not found
```

When Bash encounters an exclamation mark under certain conditions, it tries to expand the expression to the last matching command in your history.
For example:

```bash
$ echo "Hello, World"
Hello, World
$ ls
bin  foo.sh
$ !e
echo "Hello, World"
Hello, World
$ !l
ls
bin  foo.sh
```

Notice that the second command `!e` was expanded to the previous `echo "Hello, World"` statement, and `!l` is expanded to the previous `ls` statement.
This is cool, but can also be annoying if that's not what you want.
Fortunately there's a workaround.

```bash
# If you don't need variable substitution, use single quotes
$ git commit -m 'It works!'

# If you need variable substitution, keep exclamation outside the quotes
$ git commit -m "Thanks $name"\!
# Or escape with single quotes
$ git commit -m "Thanks $name"'!'
# Also works in the middle of the line
$ git commit -m "Thanks"\!" $name"
```

As far as I can tell, these solutions work in both Linux, and Git Bash in Windows.

A couple pages I found helpful when figuring this out:

* http://superuser.com/questions/133780/in-bash-how-do-i-escape-an-exclamation-mark
* http://www.unixli.com/q/answers-why-does-the-exclamation-mark-sometimes-upset-bash-19252.html