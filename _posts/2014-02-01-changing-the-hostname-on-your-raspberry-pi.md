---
title: 'Changing the hostname on your raspberry pi'
date: '2014-02-01T17:41:53+10:00'
permalink: /changing-the-hostname-on-your-raspberry-pi
author: 'James Elsey'
category:
    - Linux
tag:
    - pi
    - raspberrypi
---
By default, the hostname on a raspberry pi installation will be “raspberrypi”, which is great if you just have the one pi.

![Two raspberry pis, one with a BrickPi attached for controlling lego mindstorms](/assets/post_images/2013/double-pi.jpg)

If you’ve got more than one, then you’re going to get hostname conflicts when you attach both to your network. Fortunately its easy to correct this.

Plug the pi that you want to change hostname onto the network (leave the other unattached). That way when you ssh onto raspberrypi, you know which one it is.

Next, edit the hosts file.

```
sudo nano /etc/hosts
```

You’ll need to change the last line to whatever you want to name the pi, in my case I called it robopi

```
127.0.0.1       localhost
::1             localhost ip6-localhost ip6-loopback
fe00::0         ip6-localnet
ff00::0         ip6-mcastprefix
ff02::1         ip6-allnodes
ff02::2         ip6-allrouters

127.0.1.1       robopi
```

Exit that file and then change the hostname file

```
sudo nano /etc/hostname
```

Change it to the same name you put in the hosts file

```
robopi
```

Thats the configuration changes done, next we need to restart the hostname service, but executing:

```
sudo /etc/init.d/hostname.sh
```

Then restart the pi

```
sudo reboot
```

After that, you should be able to ping and connect to robopi:

```
Jamess-MacBook-Pro:pi Elsey$ ping robopi
PING robopi.home (192.168.0.10): 56 data bytes
64 bytes from 192.168.0.10: icmp_seq=0 ttl=64 time=1.802 ms
64 bytes from 192.168.0.10: icmp_seq=1 ttl=64 time=4.141 ms
^C
--- robopi.home ping statistics ---
2 packets transmitted, 2 packets received, 0.0% packet loss
round-trip min/avg/max/stddev = 1.802/2.971/4.141/1.170 ms
Jamess-MacBook-Pro:pi Elsey$ ssh pi@robopi
pi@robopi's password: 
Linux robopi 3.6.11+ #456 PREEMPT Mon May 20 17:42:15 BST 2013 armv6l

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Sun Dec 29 15:59:40 2013 from unknown
-bash: /etc/profile: is a directory
pi@robopi ~ $ hostname
robopi 
```

Thats it, you can connect the original “raspberrypi” to the network, or change the hostname of that too