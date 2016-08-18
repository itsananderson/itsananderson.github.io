---
layout: post
title: "The Observer Effect and Debugging: How Dev Tools Can Change Your Code's Behavior"
date: 2016-08-13 08:39:00 -0700
comments: true
categories: DevTools
---


Chrome recently added a new feature to their JavaScript debugger where when you select a piece of code and hover over it, the code is evaluated and displayed in a little popover.
In general, this is very useful.
You can check the value of a variable, or even look at the results of more complex expressions.

![Selecting Code and Hovering Shows The Result In Chrome DevTools](https://itsananderson.blob.core.windows.net/post-images/chrome-inspect-selection.gif)

This is super useful, but it can also cause problems if you're not careful about how you use it.

First, a little physics.
The <a href="https://en.wikipedia.org/wiki/Observer_effect_(physics)">observer effect</a> refers to "changes that the act of observation will make on a phenomenon being observed".
That definition precisely describes the behavior of this Chrome DevTools feature.

Consider the following example where I highlight a call to a function that has a side effect (incrementing the variable `i`).
Whenever I hover over the code, it re-runs that expression, and the value of `i` increases.

![Selecting a function call evaluates the function](https://itsananderson.blob.core.windows.net/post-images/debug-inspect-selection.gif)

This side effect is fairly benign and easy to detect, but imagine a more complex function with multiple dependencies and obscured side-effects.
Worse, you could observe the value of a [Proxy object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy) without even knowing it might have side effects.

![Hovering over Proxy properties evaluates their handler](https://itsananderson.blob.core.windows.net/post-images/chrome-proxy-hover.gif)

Obviously this also serves as a warning against side effects in proxy handlers, but the point here is that these previews can affect the behavior of your code in significant ways.

Several years ago, I wrote about [verifying your tools](/blog/2014/04/08/a-lesson-in-debugging-verify-your-tools/) after a Fiddler visualizer made a debugging session take much longer than it should have taken.
The same principal holds true here.
Like many other professions, understanding how your tools behave is an important part of software development.


