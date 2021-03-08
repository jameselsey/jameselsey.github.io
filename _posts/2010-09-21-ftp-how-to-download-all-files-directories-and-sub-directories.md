---
title: 'FTP: How to download all files, directories and sub-directories'
date: '2010-09-21T22:48:22+10:00'
permalink: /ftp-how-to-download-all-files-directories-and-sub-directories
author: 'James Elsey'
category:
    - Linux
tag: []
---
I’ve recently decided to cancel my hosting package with 1and1, as I don’t really have much use for it any more what with all the freebies Google is dishing out.

I needed to take a backup of all my files, and unfortunately mget doesn’t (or at least at the time) doesn’t support the downloading of directories, luckily there is another tool to help, [wget](http://en.wikipedia.org/wiki/Wget).

This example of wget will download all files (the -r specifies recursively) from a directory on the domain

```
wget -r ftp://account\_name:password@mydomain.com/directoryname
```