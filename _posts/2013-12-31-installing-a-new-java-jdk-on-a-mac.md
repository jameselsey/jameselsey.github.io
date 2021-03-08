---
title: 'Installing a new Java JDK on a Mac'
date: '2013-12-31T18:41:00+10:00'
permalink: /installing-a-new-java-jdk-on-a-mac
author: 'James Elsey'
category:
    - 'Mac OS X'
tag:
    - java
    - mac
    - sdk
---
Updating Java on a Mac is easy, it’s just a case of installing a new JDK and recreating the symbolic link that is used to point to the current JDK.

Download the Java .dmg for mac from the [Oracle website](http://www.oracle.com/technetwork/java/javase/downloads/index.html), then run through the installer.

That will install Java for you, but your default Java installation won’t be updated to point to the new version, but fortunately its easy to correct that

Open up a terminal and type

```
 cd /System/Library/Frameworks/JavaVM.framework/Versions/

```

You will probably see something like this

```
 lrwxr-xr-x  1 root  wheel   60 10 Dec 17:23 CurrentJDK -> /System/Library/Java/JavaVirtualMachines/1.6.0.jdk/Contents/

```

As you can see, CurrentJDK is pointing to 1.6

Delete the symbolic link

```
 sudo rm CurrentJDK

```

Then recreate it and point to the version of Java you want to use as the default

```
 sudo ln -s /Library/Java/JavaVirtualMachines/jdk1.7.0_45.jdk/Contents/ CurrentJDK
 
```

Then if you run java -version you should now see 1.7

```
 java version "1.7.0_45"
 Java(TM) SE Runtime Environment (build 1.7.0_45-b18)
 Java HotSpot(TM) 64-Bit Server VM (build 24.45-b08, mixed mode)

```