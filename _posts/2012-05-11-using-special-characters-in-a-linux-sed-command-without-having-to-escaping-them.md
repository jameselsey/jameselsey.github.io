---
title: 'Using special characters in a linux sed command without having to escaping them'
date: '2012-05-11T18:02:02+10:00'
permalink: /using-special-characters-in-a-linux-sed-command-without-having-to-escaping-them
category:
    - Linux
tag:
    - command
    - escaping
    - linux
    - sed

---
I came across a brilliant little feature of the linux [sed ](http://linux.die.net/man/1/sed "sed")command today that I wasn’t aware of, and thought it was well worth posting up about.

I’d been using sed statements inside a bash script, to trawl through property files and replace values, these were all alphanumeric values so I didn’t have any problems with the usual sed comand of

```
sed -e s/replaceThisValue/withThisValue/g
```

That worked great, but then I had some properties introduced that contained special characters, notably the forward slash and equals. I scanned the interwebz for answers and there were all sorts of complicated techniques for escaping special characters, passing them into awk and all sorts of other such nonsense, however there is a much simpler way, just use a different separator character.

Put some data into a file…

```
# this will echo the URL and save it into urls.txt
echo http://www.google.com >> urls.txt
```

Then replace it with the sed command…

```
# This will replace http://www.google.com with http://www.amazon.com
# The bit after the sed is basically a mechanism for updating the original txt file via a tmp file.
sed -e 's=http://www.google.com=http://www.amazon.com=g' urls.txt > urls.tmp && mv urls.tmp urls.txt
```

Since the old/new values above contain forward slashes, I didn’t want to bother escaping them, so I’ve just used an equals character instead of the forward slash to denote the segments of the sed command.

This also works with the at symbol, it may work for others too, but I haven’t tried

```
# The @ symbol works too!
sed -e 's@http://www.google.com@http://www.amazon.com@g' urls.txt > urls.tmp && mv urls.tmp urls.txt
```

Hope this helps!