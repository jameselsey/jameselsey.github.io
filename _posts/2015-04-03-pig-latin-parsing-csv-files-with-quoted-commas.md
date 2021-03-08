---
title: 'Pig Latin parsing CSV files with quoted commas'
date: '2015-04-03T09:11:16+10:00'
permalink: /pig-latin-parsing-csv-files-with-quoted-commas
author: 'James Elsey'
category:
    - 'Big Data'
tag:
    - apache-pig
    - csv
---
In the not too distant past, I was working on a BigData engagement using [Apache Pig](https://pig.apache.org/). I took CSV parsing for granted and expected it to just work, however if you have quoted strings with commas, it won’t behave as you’d expect.

Given:

```
1,"This is a sample sentence, same sentence, just happens to include a few commas" 
```

When you use:

```
load 'input/oneLiner.txt' using PigStorage(',') 
```

It delimits based on the comma, regardless of it being in a quoted string, so you end up with 4 fields;

```
1
This is a sample sentence
same sentence
just happens to include a few commas
```

The solution to this is to use a custom loader, such as [org.apache.pig.piggybank.storage.CSVExcelStorage()](http://pig.apache.org/docs/r0.9.1/api/org/apache/pig/piggybank/storage/CSVExcelStorage.html).

To get started with this, I had to clone the piggybank repository (collection of user defined functions, why this didn’t make it to the base release I’m not entirely sure) and build from source, unfortunately I didn’t keep any notes for this, but its relatively straightforward, see the [Apache Pig wiki page here](https://cwiki.apache.org/confluence/display/PIG/PiggyBank)