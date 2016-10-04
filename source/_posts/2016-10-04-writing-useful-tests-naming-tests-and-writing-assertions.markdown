---
layout: post
title: "Writing Useful Tests: Naming Tests and Writing Assertions"
date: 2016-10-04 04:52:58 -0700
comments: true
categories: Testing
---

Last time I blogged about [organizing your tests](/blog/2016/08/22/writing-useful-tests-organization/) so that they're easier to maintain.
Today I want to look at why you should put thought into naming your tests and writing assertions.

---

When you first write your tests, you (hopefully) have all the context of the code you're testing.
You know how the code should work, and you know what your test is trying to validate.
However, if the test starts failing 6 months down the road, you probably won't have all that context.
Writing verbose test names and assertion messages will make it much easier for you to regain that context.

Naming Things
---

It's Monday, and you just poured your first cup of "Damn Fine Coffee".

![](https://itsananderson.blob.core.windows.net/post-images/damngoodcoffee.gif)

You're checking the morning influx of email and open an email about failing tests.
You're greeted with this helpful summary:

```
Failing Tests:
  - User Creation

  Expected "false" to be "true"
```

Well, that's helpful...

![](https://itsananderson.blob.core.windows.net/post-images/more-coffee.gif)

Obviously this is a contrived example, but it illustrates two potential problems with your tests.


Problem 1: Generic Names
---

In the example above, the test is called "User Creation", which isn't very descriptive.
Now you have to find the code for the test and re-grok it, just to remember what the test does.
Only then can you figure out why it's failing.

![](https://itsananderson.blob.core.windows.net/post-images/donna-crying.gif)

It might be better to call this test something like "User creation succeeds with valid email and matching passwords" or "User creation fails with an invalid email".
If the test does more than what those detailed titles describe, it's probably time to break it into smaller tests.


Problem 2: Generic Assertions
---

The other thing you'll notice from the example is the reason the test failed: `Expected "false" to be "true"`.

That doesn't really help you track down the problem.
You know there's probably a failing assertion, but if you have many "assert" statements in your test, it may not be immediately clear whith one failed.
Adding a clear assertion message can make it much simpler to track down the part of the test that's failing.

```javascript
// Before
var succeeded = false;
assert(succeeded);
// Expected "false" to be "true"

// After
var succeeded = false;
assert(succeeded, "User creation should have succeeded");
// Expected "false" to be "true". User creation should have succeeded
```

Now it's easy to find the assertion in your code that's failing.
Plus, your "tests are failing" email will tell you why the test is failing before you've even fired up your editor.

That Was Easy
---

Ok. You've fixed your tests. Time to treat yourself to more coffee, and maybe some donuts.

![](https://itsananderson.blob.core.windows.net/post-images/donuts.gif)

*Yes, I **did** just finish watching [Twin Peaks](https://en.wikipedia.org/wiki/Twin_Peaks). Why do you ask?*
