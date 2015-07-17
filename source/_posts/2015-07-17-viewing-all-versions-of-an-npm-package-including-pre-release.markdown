---
layout: post
title: "Viewing All Versions of an NPM Package (Including Pre-Release)"
date: 2015-07-17 11:10:40 -0700
comments: true
categories:
- NPM
- Node
---

If you want to view all released versions of an npm package, there's an easy way to do it:

`npm show react-native@* version`

```
react-native@0.0.0 '0.0.0'
react-native@0.0.5 '0.0.5'
react-native@0.0.6 '0.0.6'
react-native@0.1.0 '0.1.0'
react-native@0.2.0 '0.2.0'
react-native@0.2.1 '0.2.1'
react-native@0.3.0 '0.3.0'
react-native@0.3.1 '0.3.1'
react-native@0.3.2 '0.3.2'
react-native@0.3.3 '0.3.3'
react-native@0.3.4 '0.3.4'
react-native@0.3.5 '0.3.5'
react-native@0.3.6 '0.3.6'
react-native@0.3.7 '0.3.7'
react-native@0.3.8 '0.3.8'
react-native@0.3.9 '0.3.9'
react-native@0.3.10 '0.3.10'
react-native@0.3.11 '0.3.11'
react-native@0.4.0 '0.4.0'
react-native@0.4.1 '0.4.1'
react-native@0.4.2 '0.4.2'
react-native@0.4.3 '0.4.3'
react-native@0.4.4 '0.4.4'
react-native@0.5.0 '0.5.0'
react-native@0.6.0 '0.6.0'
react-native@0.7.1 '0.7.1'
```

However, this doesn't show pre-release versions. If you want to see *everything*, there's an equally easy (but undocumented) command:

`npm show react-native versions`

```
[ '0.0.0',
  '0.0.5',
  '0.0.6',
  '0.1.0',
  '0.2.0',
  '0.2.1',
  '0.3.0',
  '0.3.1',
  '0.3.2',
  '0.3.3',
  '0.3.4',
  '0.3.5',
  '0.3.6',
  '0.3.7',
  '0.3.8',
  '0.3.9',
  '0.3.10',
  '0.3.11',
  '0.4.0',
  '0.4.1',
  '0.4.2',
  '0.4.3',
  '0.4.4',
  '0.5.0-rc1',
  '0.5.0',
  '0.6.0-rc',
  '0.6.0',
  '0.7.0-rc',
  '0.7.0-rc.2',
  '0.7.1',
  '0.8.0-rc' ]
```

This is super useful for finding what beta/pre-release versions of a package are available.

You can also run `npm show react-native versions --json` for machine readable output.
