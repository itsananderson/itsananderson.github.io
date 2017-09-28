---
layout: post
title: "Consuming and Searching CSV Logs With JSON Tools"
date: 2017-09-27 21:47:27 -0700
comments: true
categories: JSON
---

If you ever find yourself working with logs or traces stored as CSV files,
it's fairly trivial to convert them to JSON to simplify searching and manipulating them.
This blog post describes some tools that can help you filter and format the logs into something more useful.

The two tools we'll be using are `csv2json` and `json`.
These are Node modules, so you'll need to install Node first,
then run the following command to install the two packages globally.

```
npm install -g csv2json json
```

These tools should now be on your path.
To confirm, try running `csv2json --help`

You can use the `csv2json` to convert the CSV log files into JSON files.
The JSON format places the column headers next to the column values,
which is less space efficient, but makes it much easier to read the file.

```
csv2json trace.csv trace.json
```

The JSON traces will then look something like this:

```
[
{"PreciseTimeStamp":"3/24/2017 12:25:17 PM","ComponentGuid":"bd3facb0-1775-590d-e3ab-59f56bb23206","EventName":"DownloadStarted","AppId":"cd56de80-67c6-414a-a0af-f6b22a47b6d7"},
{"PreciseTimeStamp":"3/24/2017 12:35:17 PM","ComponentGuid":"bd3facb0-1775-590d-e3ab-59f56bb23206","EventName":"DownloadCompleted","AppId":"cd56de80-67c6-414a-a0af-f6b22a47b6d7"}
...
...
...
]
```

Now at least the column names/values are closer together, so it's easier to see what column type you're dealing with.
However, it's still not easy to visually parse the data.
That's where the `json` tool comes in.
In its most basic form, it pretty-prints json, making it easier to visually parse:

```
cat trace.json | json
[
  {
    "PreciseTimeStamp": "3/24/2017 12:25:17 PM",
    "ComponentGuid": "bd3facb0-1775-590d-e3ab-59f56bb23206",
    "EventName": "DownloadStarted",
    "AppId": "cd56de80-67c6-414a-a0af-f6b22a47b6d7"
  },
  {
    "PreciseTimeStamp": "3/24/2017 12:35:17 PM",
    "ComponentGuid": "bd3facb0-175-590d-e3ab-59f56bb23206",
    "EventName": "DownloadCompleted",
    "AppId": "cd56de80-67c6-414a-a0af-f6b22a47b6d7"
  }
]
```

Now you can more easily scan down the columns, but it takes up a ton of space, and you probably don't care about most of the columns.
If you only want a few columns, you can use the `json -a <ColumnName1> <ColumnName2>` command to output specific columns in plaintext.

```
type trace.json | json -a PreciseTimeStamp EventName
3/24/2017 12:25:17 PM DownloadStarted
3/24/2017 12:35:17 PM DownloadCompleted
```

If you don't want the intermediate JSON file, you can also pipe the output of `csv2json` directly to the `json` command.
However, if you plan to run multiple calls to `json`, you will save execution time by only calling `csv2json` once.

```
csv2json trace.csv | json -a PreciseTimeStamp EventName
```

Also, if you already have a json file, you can provide its path to the `json` tool directly rather than using the `cat` command.
This is really just a matter of preference:

```
json -f trace.json -a PreciseTimeStamp EventName

# same as

cat trace.json | json -a PreciseTimeStamp EventName
```  

If you want to filter the logs, you can use the `-c` flag.
The syntax is JavaScript, and the `this` variable contains the JSON object for the specific row being filtered.

For example, to filter to a specific `EventName`, you could use something like this:

```
cat trace.json | json -a PreciseTimeStamp EventName -c 'this.EventName == "DownloadCompleted"'
3/24/2017 12:35:17 PM DownloadCompleted
```

There's lots more that the `json` tool can do. Check out the [http://trentm.com/json/](full docs here).
