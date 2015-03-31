---
layout: post
title: "Obvious Code"
date: 2015-03-27 06:10:17 -0500
comments: true
categories: JavaScript
---

A week or two ago, I got this feedback on a code review:

> You're using `foo ? foo : ""` here.
> It would be shorter and more obvious if you wrote it as `foo || ""`

This was valid feedback, and I updated the code.

After I'd checked in my changes, I got the following IM from a senior member of my team:

> I see you did `foo || ""`? What does the `||` mean?

:)

This was a good reminder of two things:

* "Obvious code" is a subjective concept
* No matter how experienced you are with something, you'll still find blind spots from time to time

*P.S.* If that syntax is also new to you, it's taking advantage of how JavaScript does "or".
A JavaScript *or* returns the first *truthy* value, or the last value if none are truthy:

e.g. `(false || 0) === 0` and `(0 || false) === false` and `(foo || "") === ""` (assuming foo is "falsey")

Many JavaScript developers use this behavior as a shortcut for setting default values, because `null` and `undefined` are falsey.
