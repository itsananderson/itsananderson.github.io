---
layout: post
title: "Storing Data as Newline Delimited JSON Entries"
date: 2014-07-16 13:48:29 -0700
comments: true
categories: JSON
---

You can store data as newline delimited JSON entries.
Since JSON encodes newline characters, you can use newlines to separate entries.

For more details, read on.

Exporting Large Data
---

In a recent project, I needed to export data, and then import it into another system.
I decided to go with JSON as the export format, because it was structured data.
The problem was, the data could be extremely large, so loading it all into memory and generating a single JSON object wasn't an option.

As a first solution to the problem, I considered "fake" streaming to the file with the following pseudocode:

```
open the file
write "["
foreach entry:
	if not first entry:
		write ","
	write JSON serialized entry
write "]"
close the file
```

This got the job done, but what about when I import the data?
Since I wrote the data as one giant JSON array, I'd have to load the entire thing to deserialize.
(In Node, I could use a streaming JSON parser, but this project was in PHP).

This means there's a limit to the amount of data I can import.
Even on a host where I can control PHP's memory limit, there's still a limitation to how much data can reasonably be loaded into memory.

Exporting Newline Delimited Data
---

I finally realized that I was missing something important.
JSON serializes newlines as "\n", which means actual newlines can be safely used to delimit each JSON entry.
(Assuming you don't "pretty print" your JSON).

The new export method is as follows:

```
open the file
foreach entry:
	if not first entry:
		write newline
	write JSON serialized entry
close the file
```

Already simpler. Yay.

Importing Newline Delimited Data
---

Now that we've exported the data, it's time to import it.
In PHP, this is still a challenge, because there's no elegant solution for streams.
The closest you can get is the [`stream_get_line`](http://php.net/manual/en/function.stream-get-line.php) function.
The problem with this function is that you have to specify a max length of characters to read.
If the line is longer than those characters, you have to detect that, and keep searching until you get to the newline.
There's a [comment on the manual that outlines the solution](http://php.net/manual/en/function.stream-get-line.php#109339).

In pseudocode:

```
open the file
while get line from file:
	import entry
close the file
```
Again, fairly simple (ignoring caveats with "get line from file").
The great thing about this solution is that we only have to load at most one entry at a time into memory.

Bonus: Inspecting JSON Data From CLI
---

If you want to inspect the data that you've exported, you can use the handy "[json](http://npmjs.org/package/json)" tool (requires Node).
Not only does it pretty print, but it allows you to pull out specific data, and supports newline delimited JSON entries with the `-g` flag.

```
$ cat test.json
{"name":"alice"}
{"name":"bob"}
{"name":"eve"}
```
```
$ cat test.json | json -g
[
  {
    "name": "alice"
  },
  {
    "name": "bob"
  },
  {
    "name": "eve"
  }
]
```
```
$ cat test.json | json -ga name
alice
bob
eve
```
```
$ cat test.json | json -ga nameLength name -e 'this.nameLength = this.name.length'
5 alice
3 bob
3 eve
```

[Full Documentation](http://trentm.com/json/)
