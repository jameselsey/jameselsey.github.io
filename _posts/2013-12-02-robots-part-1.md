---
title: 'Robots! (part 1)'
date: '2013-12-02T22:47:19+10:00'
permalink: /robots-part-1
author: 'James Elsey'
category:
    - Robotics
tag:
    - android
    - brickpi
    - java
    - lego
    - python
    - raspberrypi
    - robot
---
Inspired by the android controlled lego robots I saw at DroidCon UK this year, and with difficulty finding a use for my raspberry pi, I’ve decided to have a go at building a robot that I can control via an android app. Having a 24 hour flight home from Australia at the weekend, I’ve had plenty of time to think about how I might approach this task (or challenge as I refer to it as I’ve no prior experience with robotics / socket programming).

![Lego robot powered by the BrickPi](http://mashable.com/wp-content/uploads/2013/05/BrickPi-tank.jpg)

My plan is to have a python socket server running on the pi. This will provide a socket that an android client can invoke commands on. This python server will also interact with the python scripts that the [BrickPi](http://www.dexterindustries.com/BrickPi/) uses to control lego motors.

Whilst most of my experience revolves around Java, I’ve opted for python for the following reasons

- Python is supported on the pi out of the box, no need to mess around with installing Java
- The BrickPi has support for Python (and C)
- I feel like learning something new

### Baby steps…

Starting simple, I thought it best to create a simple script that listens on a socket, and then create a client that sends it some data to print out to the console. Once I have this working I can expand on it and make the client more sophisticated (an android app for example) and also enhance the server so it can handle different types of commands.

**Server**  
There are plenty of example python scripts online, I found a good one [here](http://pythonadventures.wordpress.com/2013/07/06/a-basic-socket-client-server-example/) and tweaked it slightly to remove the parts I don’t want. (full credit to [pythonadventures](http://pythonadventures.wordpress.com/2013/07/06/a-basic-socket-client-server-example/)!)

```python
#!/usr/bin/env python
# server.py

import socket
import select
import Queue
from threading import Thread
import sys

class ProcessThread(Thread):
    def __init__(self):
        super(ProcessThread, self).__init__()
        self.running = True
        self.q = Queue.Queue()

    def add(self, data):
        self.q.put(data)

    def stop(self):
        self.running = False

    def run(self):
        q = self.q
        while self.running:
            try:
                # block for 1 second only:
                value = q.get(block=True, timeout=1)
                process(value)
            except Queue.Empty:
                sys.stdout.write('.')
                sys.stdout.flush()
        #
        if not q.empty():
            print "Elements left in the queue:"
            while not q.empty():
                print q.get()

t = ProcessThread()
t.start()

def process(value):
    print value

def main():
    s = socket.socket()
    host = socket.gethostname()
    port = 3033
    s.bind((host, port))

    print "Server listening on port {p}...".format(p=port)

    s.listen(5)                 # Now wait for client connection.

    while True:
        try:
            client, addr = s.accept()
            ready = select.select([client,],[], [],2)
            if ready[0]:
                data = client.recv(4096)
                t.add(data)
        except KeyboardInterrupt:
            print
            print "Stopping server."
            break
        except socket.error, msg:
            print "Socket error %s" % msg
            break

    cleanup()

def cleanup():
    t.stop()
    t.join()

if __name__ == "__main__":
    main()
```

The socket is bound to a port, and then continually listens for incoming data. Once some data is received, it is added onto a queue, which is then sequentially executed.

**Client**  
The client is is fairly straightforward, it involves opening a connection on a socket and writing data to it, a few lines of Java code.

```java
public static void main(String[] args) throws IOException {

        InetAddress address = InetAddress.getLocalHost();
        Socket socket = new Socket(address, 3033);

        BufferedOutputStream bos = new BufferedOutputStream(socket.getOutputStream());

        OutputStreamWriter osw = new OutputStreamWriter(bos, "US-ASCII");

        System.out.println("Sending message...");

        osw.write("Hello!");
        osw.flush();
    }
```

Thats it for now, you can checkout my code on [github](https://github.com/jameselsey/robots/commit/71b71538ce743eb9f7622a10d4ede2247004a84f) (or just copy/paste the above) and run the client and server and see it in action.

I’ll start on the client next..