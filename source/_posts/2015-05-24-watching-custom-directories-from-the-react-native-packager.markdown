---
layout: post
title: "Watching Custom Directories From the React Native Packager"
date: 2015-05-24 18:51:57 -0700
comments: true
categories:
- React Native
---

React Native projects ship with a packager that builds and serves your JavaScript resources. It also watches your project folder for changes, and rebuilds. However, if you link a Node module from a different directory, the packager won't see changes that are made to that package.

It's fairly easy to specify custom folders for the packager to watch. Since the React Native client sets up an `npm start` command when it generates the project, you can simply modify that script to watch additional folders.

```
"start": "node_modules/react-native/packager/packager.sh --root ~/custom-package/"
```

Unfortunately, when you launch the project in Xcode, it runs the packager directly, rather than running the `npm start` command, but if you manually run `npm start` from a terminal window, React Native will use that rather than launching a new instance of the packager. I have an [issue opened on the React Native project](https://github.com/facebook/react-native/issues/1396), suggesting that they run `npm start` from Xcode, but we'll see if it goes anywhere.
