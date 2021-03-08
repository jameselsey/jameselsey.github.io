---
title: 'BrickPi Lego robot takes its first steps!'
date: '2014-01-07T18:32:22+10:00'
permalink: /brickpi-lego-robot-takes-its-first-steps
author: 'James Elsey'
category:
    - Robotics
tag:
    - brickpi
    - lego
    - pi
    - python
    - robotics
---
A little while back I wrote a [post](/robots-part-1) about creating a python script to control a brickpi robot, now that I actually have the brickpi components, motors and cables and have actually tried it out, I’ve made a few improvements to that script to make it work.


#### Installing Raspbian

Firstly, I had to revisit the raspbian installation. With the brickpi bundle I bought from Dexter Industries, it came with a pre-installed raspbian SD card. I’m not sure what was wrong, but I wasn’t able to boot into it, there were startup errors regarding /etc/init.d. A quick Google didn’t help, so I decided to flash a new SD card with a more recent version of raspbian.

It’s important to note that the stock raspbian image won’t automatically work with the brickpi, as some modifications are required in order to control I/O for the motors. You can make these modifications yourself as there are instructions on the brickpi [website](http://www.dexterindustries.com/BrickPi/getting-started/pi-prep/), but it’s far easier to just download the pre-modified raspbian from dexter industries.

This was quite simple, on a mac you can erase and format an SD card using Disk Utility, after that, use these commands to create the SD card image:

```
diskutil list
diskutil unmountdisk /dev/disk1
dd if=2013.07.27_BrickPi.img of=/dev/disk1 bs=2m
```

diskutil list will show you the drives attached, in my case the SD card was disk1 but it may vary for you. It takes around 10 minutes to flash the SD card so go make a brew.

#### Configuring WiFi

Setting up wifi on the pi is pretty easy, I have a USB wifi adapter, so it was just a case of configuring a wireless interface, which can be done by adding the following to /etc/network/interfaces

```
sudo nano /etc/network/interfaces
add
iface wlan0 inet dhcp
wpa-ssid "NETWORK_ID_HERE"
wpa-psk "NETWORK_PASSWORD_HERE"
```

Then do a sudo reboot

#### Expanding the SD card

In order to expand the root partition and make use of the entire SD card, run the following command and follow the menus to expand SD card

```
sudo raspi-config
```

Then do a sudo reboot


#### Setting up the brickpi dependencies

SSH onto the pi and run the following

```
mkdir brickpi
cd brickpi/
git clone https://github.com/DexterInd/BrickPi_Python.git
cd BrickPi_Python
sudo apt-get install python-setuptools
sudo python setup.py install
```

This will clone the BrickPi python [repository](https://github.com/DexterInd/BrickPi_Python) (which contains a load of examples), install python-setuptools and install the brickpi module, so you can import Brickpi globally in your python scripts.

#### Controlling the robot

After testing out the brickpi examples, it was quite clear that I’d need to modify my [original](/robots-part-1) python script. I needed 2 threads.

- Thread 1 would continually update the motors every 200ms, otherwise the motors would turn briefly and then remain still.
- Thread 2 would process incoming commands and set the motor speeds.

Other than the threading code, the script is quite easy to follow

```python
from BrickPi import *   #import BrickPi.py file to use BrickPi operations
import threading
import socket
import select
import Queue
from threading import Thread
import sys

BrickPiSetup()  # setup the serial port for communication
BrickPi.MotorEnable[PORT_A] = 1 #Enable the Motor A
BrickPi.MotorEnable[PORT_D] = 1 #Enable the Motor D
BrickPiSetupSensors()   #Send the properties of sensors to BrickPi

running = True

#This thread is used for keeping the motor running while the main thread waits for user input
class BrickPiThread (threading.Thread):
    def __init__(self, threadID, name, counter):
        threading.Thread.__init__(self)
        self.threadID = threadID
        self.name = name
        self.counter = counter
    def run(self):
        while running:
            BrickPiUpdateValues()       # Ask BrickPi to update values for sensors/motors
            time.sleep(.2)              # sleep for 200 ms

brickPiThread = BrickPiThread(1, "BrickPiThread", 1)                #Setup and start the thread
brickPiThread.setDaemon(True)
brickPiThread.start()

class ProcessCommandThread(Thread):
    def __init__(self):
        super(ProcessCommandThread, self).__init__()
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
        if not q.empty():
            print "Elements left in the queue:"
            while not q.empty():
                print q.get()

commandThread = ProcessCommandThread()
commandThread.start()

def process(value):
    print "Processing [{v}]".format(v=value)

    # Left side
    if value == 'L-FORWARD-Start':
        print "L-FORWARD-Start"
        BrickPi.MotorSpeed[PORT_A] = 200
    elif value == 'L-FORWARD-Stop':
        print "L-FORWARD-Stop"
        BrickPi.MotorSpeed[PORT_A] = 0
    elif value == 'L-BACK-Start':
        print "L-BACK-Start"
        BrickPi.MotorSpeed[PORT_A] = -200
    elif value == 'L-BACK-Stop':
        print "L-BACK-Stop"
        BrickPi.MotorSpeed[PORT_A] = 0
    # Right side
    if value == 'R-FORWARD-Start':
        print "R-FORWARD-Start"
        BrickPi.MotorSpeed[PORT_D] = 200
    elif value == 'R-FORWARD-Stop':
        print "R-FORWARD-Stop"
        BrickPi.MotorSpeed[PORT_D] = 0
    elif value == 'R-BACK-Start':
        print "R-BACK-Start"
        BrickPi.MotorSpeed[PORT_D] = -200
    elif value == 'R-BACK-Stop':
        print "R-BACK-Stop"
        BrickPi.MotorSpeed[PORT_D] = 0

def main():
    s = socket.socket()
    #host = socket.gethostname()
    host = "192.168.0.10"
    port = 3033
    s.bind((host, port))

    print "Server listening on port {p}...".format(p=port)

    s.listen(5)                 # Now wait for client connection.

    while True:
        try:
            client, addr = s.accept()
            ready = select.select([client, ], [], [], 2)
            if ready[0]:
                data = client.recv(4096)
                commandThread.add(data)
        except KeyboardInterrupt:
            print
            print "Stopping server."
            break
        except socket.error, msg:
            print "Socket error %s" % msg
            break

    cleanup()

def cleanup():
    commandThread.stop()
    commandThread.join()

if __name__ == "__main__":
    main()
```

Paste that into server.py then run the following:

```
sudo python server.py
```

You should see some output in the terminal as it’ll print out any incoming commands. Then its up to you to connect onto that socket with a client of your choice. I’m using an android app (which I’ve detailed [here](/robots-part-2-the-android-client), but you could use something as simple as the following Java client

```java
import java.io.*;
import java.net.Socket;

public class Runner {

    public static void main(String[] args) throws IOException {

        Socket socket = new Socket("raspberrypi", 3033);

        BufferedOutputStream bos = new BufferedOutputStream(socket.getOutputStream());

        OutputStreamWriter osw = new OutputStreamWriter(bos, "US-ASCII");

        osw.write("L-FORWARD-Stop");
        osw.flush();
    }
}
```

Resources
---------

- [Server project on github](https://github.com/jameselsey/pi-robot-server)
- [Android client on github](https://github.com/jameselsey/pi-robot-android)

Any thoughts / suggestions? Please comment below!