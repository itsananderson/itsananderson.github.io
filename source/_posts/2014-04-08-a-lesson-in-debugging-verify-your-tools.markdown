---
layout: post
title: "A Lesson in Debugging: Verify Your Tools"
date: 2014-04-08 08:10:41 -0700
comments: true
categories: Debugging JavaScript Fiddler
---

This is a cautionary tale

Yesterday I wasted a day of dev time.
Worse than that, I was pairing with [two](http://www.davidruttka.com/) of my [co-workers](http://msarchet.com/) most of the day, so in the end we wasted about 2.5 days of dev time.

We spent the day debugging an "off by one (and sometimes two)" error.

David, one of my coworkers, [also blogged this experience](http://www.davidruttka.com/blog/2014/04/09/principles-of-debugging-a-postmortem/).
It's an entertaining read.
Check it out.

The Problem
-----------

The bug seemed simple.
They always do.
When you requested something by Id from our API, it returned with almost valid data.
Except that the Id property in the JSON (a long) was frequently incremented or decremented by 1 or 2.

We originally saw the issue in an Angular app.
We were suspicious about potential JavaScript numeric issues, so of course we opened up Fiddler to get at the "real" data

The Fiddler Inspection
----------------------

When we inspected the requests in Fiddler and looked at the JSON viewer, same "off by [1-2]" issue.
In our minds, this confirmed that Angular and JavaScript weren't the issue.

After going through the "disable stuff until it works" process with our API, we were still seeing the same problem.
The only thing that fixed the issue was having Fiddler request XML instead of JSON. "Ahh", we thought, "The issue must be with our JSON serializer"

Checking the Serializer
-----------------------

We spent a few minutes in LINQPad performing serializations with Json.NET, duplicating the problematic object as closely as possible.
The object serialized without issue, which confirmed in our minds that Json.NET does not, in fact, mangle long values during serialization

The Reduction
-------------

We decided to build a reduction by creating an empty WebApi project and adding components until we reproduced the issue.
We added a dummy controller that returns an object identical to the one with the serialization issues.
We opened the new URL in the browser, and the serialization issues weren't there.
We should have checked the reduction in Fiddler, but we didn't.

Patterns Emerge
---------------

Early on in our debugging session, I noticed that even Ids either remained unchanged or incremented/decremented by two.
Odd Ids, on the other hand, always either incremented or decremented by one.

I initially identified a simplified version of that pattern where even Ids don't change, so when counter-examples emerged, I abandoned the search for a pattern.

As the afternoon wore on, we decided to generate a consecutive sequence of Ids to see if we could identify a pattern.
What we discovered is that for large Ids, the numbers began "snapping" to multiples of four.
Small Ids (like, say, 207) didn't change.

The Final Puzzle Piece
----------------------

While David spun a series of object serializations through LINQPad, I began combining keywords in Bing to see if I could find anyone else with the same issue.

Around the time David realized that LINQPad was spitting out serialized Ids with correct values, I discovered [this StackOverflow post on "correctly" serializing longs](http://stackoverflow.com/questions/6571339/how-to-correctly-serialize-c-sharp-long-number-using-controllers-json-method-in).
As the answer explains, JavaScript doesn't have longs.
All numbers are 64 bit floats.
We'd considered this, but had dismissed it because Fiddler was showing the same issue.

Then it hit me.
Fiddler is written in JScript, which means it has the same 64 bit float limitations.

By implicitly trusting Fiddler's JSON viewer, we assumed the problem was incorrect data coming from our server.
When we finally looked at the raw data in Fiddler, we saw that the correct Ids were coming across the wire.
Fiddler's JSON viewer was parsing the long into a 64 bit float and tossing away the last 2 bits, which effectively "snapped" all Ids to multiples of 4.

The problem wasn't with our API directly.
The problem was the way JavaScript and JScript handle large numbers.

Lesson Learned
---------------

When debugging an issue like this, look at the raw data.
Not some data viewer over the data.
The *raw* data