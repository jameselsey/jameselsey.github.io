---
title: 'How to keep checking if port is open, by issuing socket connects'
date: '2011-01-27T19:53:41+10:00'
permalink: /how-to-keep-checking-if-port-is-open-by-issuing-socket-connects
author: 'James Elsey'
category:
    - Python
tag:
    - connect
    - host
    - linux
    - localhost
    - network
    - port
    - python
    - script
    - socket
---
I recently need to check if a particular application is online and listening on a particular set of ports. I figured that the best way to do this was to periodically issue some socket connects onto those ports. If I got an exception, then clearly there would be an issue.

This was all running on a CentOS Linux machine, so I opted for a Python script, have a look at my sample below

```python
# Socket Connect Script
# Author : James Elsey
# Date : 27 January 2011

# Imports
import socket
import sys
import time
import datetime

# Configure these as required..
remote_host = "127.0.0.1"

print "About to start running script..."

# run continuously
while True:
        time.sleep(60)
        for remote_port in [80,8080]:
                # Setup socket
                sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
                sock.settimeout(60)
                now = datetime.datetime.now()
                now_text = now.strftime("%Y-%m-%d %H:%M:%S")

                try:
                        # Issue the socket connect on the host:port
                        sock.connect((remote_host, remote_port))
                except Exception,e:
                        print "%s %d closed - Exception thrown when attempting to connect. " % (now_text, remote_port)
                else:
                        print  "%s %d open" % (now_text, remote_port)
                sock.close()
```

So there you have it, take it and modify as you please! You could remove the while True and setup a for loop to check for a specific number of times, or leave the continual loop in and create another method of breaking out, for me I just wanted to leave it running in a putty window and watch for changes.

Also, to run it, just use save it into a .py file and call “python myFile.py”

Happy coding