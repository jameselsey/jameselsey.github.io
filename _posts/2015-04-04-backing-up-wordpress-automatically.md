---
title: 'Backing up wordpress automatically'
date: '2015-04-04T09:13:42+10:00'
permalink: /backing-up-wordpress-automatically
author: 'James Elsey'
category:
    - Blog
tag:
    - backup
    - dropbox
    - wordpress
---
I’ve had some difficulties getting the [BackWPup plugin](https://wordpress.org/plugins/backwpup/) to work, it seems that you can’t backup everything in one job as the script takes too long to run and the server will terminate it, causing a failed job.

The 2 errors I was seeing are

1. WARNING: Job restart due to inactivity for more than 5 minutes.
2. ERROR: Uploaded file size and local file size don’t match.

Which led me to [this post](http://hazenet.dk/2013/07/23/avoid-issues-with-backup-of-wordpress-using-backwpup), which recommends a better way to structure your backup jobs, basically just split them out into content, plugins, install etc so they finish within the timeout threshold.