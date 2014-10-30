---
layout: post
title: "Ignoring JavaScript Libraries While Debugging"
date: 2014-10-28 14:33:06 -0700
comments: true
categories: JavaScript Debugging
---

Debugging front-end JavaScript can sometimes be painful.
When you rely on third-party libraries and frameworks, you find yourself stepping back and forth between the library's code and your own.
Most of the time, it's safe to assume that these third-party libraries are working correctly, so ignoring them can make it easier to keep your own code in your head.

Fortunately for us, Chrome, Firefox, and Internet Explorer each provide a way to ignore library code.

### Chrome

In chrome, you can ignore a specific script by right clicking it in the "Sources" tab.

![](http://itsananderson.blob.core.windows.net/post-images/chrome-blackbox-script.png)

You can view the currently ignored patterns in the devtools settings page.

![](http://itsananderson.blob.core.windows.net/post-images/chrome-blackbox-patterns.png)


### Firefox

Firefox is similar to Chrome, but you select the script in the "Debugger" tab, and click the eye icon to "Toggle Black Boxing".

![](http://itsananderson.blob.core.windows.net/post-images/firefox-blackbox-script.png)

### Internet Explorer

Internet explorer uses a different term, but the concept is the same.

![](http://itsananderson.blob.core.windows.net/post-images/internet-explorer-mark-as-library.png)

You can edit the exclusion list in:

`%APPDATA%\..\LocalLow\Microsoft\F12\header\MyCode.json`

or

`%APPDATA%\..\Local\Microsoft\F12\header\MyCode.json`

More info is available in the <a href="http://msdn.microsoft.com/en-us/library/ie/dn255007(v=vs.85).aspx#just_my_code">F12 tools documentation</a>.
