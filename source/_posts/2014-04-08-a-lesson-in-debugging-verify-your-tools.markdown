---
layout: post
title: "A Lesson in Debugging: Verify Your Tools"
date: 2014-04-08 08:10:41 -0700
comments: true
categories: Debugging, JavaScript, Fiddler
---

This is a cautionary tale

Yesterday I wasted a day of dev time.
Worse than that, I was pairing with [two](http://www.davidruttka.com/) [co-workers](http://msarchet.com/), so in the end we wasted around 2.5 days of dev time.

We spent the day debugging an "off by one (and sometimes two)" error.

The bug seemed simple.
They always do.
When you requested something by ID from our API, it returned with almost valid data.
Except that the ID property in the JSON (a long) was frequently incremented or decremented by 1 or 2.

We originally saw the issue in an Angular app.
We were suspicious about potential JavaScript numeric issues, so of course we opened up Fiddler to get at the "real" data

When we inspected the requests in Fiddler and looked at the JSON viewer, same "off by [1-2]" issue.
In our minds, this confirmed that Angular/JavaScript weren't the issue.

After going through the "disable stuff until it works" process with our API, we were still seeing the same problem.
The only thing that fixed the issue was having Fiddler request XML instead of JSON. "Ahh", we thought, "The issue is with our JSON serializer"

We decided to build a reduction by creating an empty WebApi project and adding components until we reproduced the issue.
We added a dummy controller that returns an object identical to the one with the serialization issues.
We opened the new URL in the browser, and the serialization issues weren't there.




The main lesson we learned: When debugging an issue like this, look at the raw data.
Not some data viewer over the data.
The *raw* data