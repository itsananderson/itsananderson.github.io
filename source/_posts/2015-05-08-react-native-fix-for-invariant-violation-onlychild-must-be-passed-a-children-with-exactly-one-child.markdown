---
layout: post
title: "React Native: Fix for \"onlyChild must be passed a children with exactly one child\""
date: 2015-05-08 07:37:37 -0700
comments: true
categories:
- React
- React Native
---

If you've run into `onlyChild must be passed a children with exactly one child` in React Native there's a simple fix.

All of the "Touchable" components in React Native do an `onlyChild` check on their children, which throws an error unless there's exactly one child.

```javascript
return (
	<TouchableHighlight>
	</TouchableHighlight>
); // Error: onlyChild must be passed a children with exactly one child
```

Adding a child to the TouchableHighlight will fix the error

```javascript
return (
	<TouchableHighlight>
		<Text>foo</Text>
	</TouchableHighlight>
); // OK
```
But adding multiple children will also cause an error

```javascript
return (
	<TouchableHighlight>
		<Text>foo</Text>
		<Text>bar</Text>
	</TouchableHighlight>
); // Error: onlyChild must be passed a children with exactly one child
```

A quick search through the React Native code reveals the components that verify onlyChild:

* TouchableBounce
* TouchableHighlight
* TouchableOpacity
* TouchableWithFeedback

Hopefully React Native can eventually provide a better message for this issue, but until then, at least this blog post should pop up when you Google the error.
