---
title: 'Stop Mac OS X creating hidden files when using the tar command'
date: '2012-08-19T16:49:07+10:00'
permalink: /stop-mac-os-x-creating-hidden-files-when-using-the-tar-command
author: 'James Elsey'
category:
    - 'Mac OS X'
tag:
    - bash
    - linux
    - mac
    - 'os x'
    - tar
---
Last week I found something quite frustrating with the tar command on Mac OS X, it likes to put [hidden files into archives](http://superuser.com/questions/259703/get-mac-tar-to-stop-putting-filenames-in-tar-archives) when you tar them up, it doesn’t give you any warning, just does it.

Creating a tar, and then having a look at its contents, you’ll see something like this :

```
JamesMac:staging-area JElsey$ tar -tf MyApplication.tar.gz 
src/
src/._MyApplication.cmd
src/MyApplication.cmd
src/._MyApplication.properties
src/MyApplication.properties
src/lib/
src/lib/._anExternalJar.jar
src/lib/anExternalJar.jar
```

Notice the files prefixed with `._`.

You can quite easily stop this, by setting the following environment variable (I prefer to set this up in the bashrc profile):

```
COPYFILE_DISABLE=1; export COPYFILE_DISABLE
```

Then, tar up the files again, and you should see those hidden files no longer.

```
JamesMac:staging-area JElsey$ tar -tf MyApplication.tar.gz 
src/
src/MyApplication.cmd
src/MyApplication.properties
src/lib/
src/lib/_anExternalJar.jar
```

Failing that, you could also install GNU tar instead of the Mac version.