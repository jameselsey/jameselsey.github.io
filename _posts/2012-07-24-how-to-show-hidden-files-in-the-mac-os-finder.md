---
title: 'How to show hidden files in the Mac OS Finder'
date: '2012-07-24T20:35:57+10:00'
permalink: /how-to-show-hidden-files-in-the-mac-os-finder
author: 'James Elsey'
category:
    - 'Mac OS X'
tag:
    - finder
    - 'hidden files'
    - mac
    - 'os x'
---
Frustrated, that I recently couldn’t find my maven settings.xml file because the Mac OS X Finder doesn’t show hidden files by default, I found that the following can correct that

1. Open a terminal
2. Type this : **defaults write com.apple.finder AppleShowAllFiles TRUE**
3. Kill any open finder sessions by typing (note the capital F) : **killall Finder**
4. Re open the Finder, and you should now be able to see hidden files.

You can also reverse the above by changing TRUE to FALSE.

Hope this helps