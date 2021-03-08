---
title: 'Recursively deleting .svn directories'
date: '2012-07-24T21:00:58+10:00'
permalink: /recursively-deleting-svn-directories
author: 'James Elsey'
category:
    - Linux
tag:
    - bash
    - linux
    - mac
    - subversion
---
Messing around with your subversion directories and fed up of the .svn hidden folders laying around? If you try and checkin some directories that contain a .svn folder from some other repository, you’re going to have a whole world of pain trying to fix it (speaking from first hand experience here)

The easiest way to clear up rogue .svn directories is to run this command (on linux or mac). It will recursively find all directories named .svn, and pass them in for removal.

```bash
rm -rf `find . -type d -name .svn`
```

On a Windows 7 environment, I find the easiest way is to just open Windows Explorer in the root directory that you’re interested in, and search for .svn in the top right hand search bar, then highlight all results for .svn and delete, easy :)

Hope this helps