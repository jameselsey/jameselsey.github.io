---
title: 'Raspberry Pi torrent box with VPN, a guide that actually works'
date: '2020-01-19T09:47:14+10:00'
status: publish
permalink: /raspberry-pi-torrent-box-with-vpn-a-guide-that-actually-works
author: 'James Elsey'
category:
    - 'Raspberry Pi'
tag:
    - linux
    - torrent
    - vpn
post_format: []
---
In this post I’m going to show you how to setup a torrent box (transmission) on a raspberry pi, that sits on a VPN so your privacy is protected. If the VPN disconnects, it will automatically stop the torrent service so you can rest assured that you are protected.

I’ve setup a torrent box on my pi a few times, but I could never quite get them working the way I wanted. I had a terrible internet connection and the VPN would always be dropping out. Fast forward to 2019 and I finally got the NBN and had a spare pi3 so figured I’d give it another shot. Here is how I did it. This definitely works, I’ve just run through my own post with a new 64gb card from scratch.  
*Disclaimer: Whilst using a torrent client is not illegal, the content you download may be. You can download linux distros this way, but if you’re sailing the seven seas that’s on your own doing!*

What I used
-----------

- Raspberry Pi 3.
- 16gb microSD, I would recommend going much larger, but it was all I had to hand.
- VPN subscription, I am using PIA (PrivateInternetAccess), but others would work.
- A few glasses of whisky (optional)

Setting up OpenVPN
------------------

I’m assuming that by reading this, you have some basic knowledge of the pi and have already installed raspbian and are either connected in via SSH or an old fashioned keyboard and monitor.

Let me first introduce you to this little command that will help you check your IP and location as seen on the internet.

```
curl https://freegeoip.app/xml/
```

I forget that command all the time, so I’ve wrapped it up into a file called checkip, I can run it like so:

```
./checkip

<Response>
	<IP>180.150.38.snip</IP>
	<CountryCode>AU</CountryCode>
	<CountryName>Australia</CountryName>
	<RegionCode>VIC</RegionCode>
	<RegionName>Victoria</RegionName>
	<City>Hughesdale</City>
	<ZipCode>3166</ZipCode>
	<TimeZone>Australia/Melbourne</TimeZone>
	<Latitude>-37.snip</Latitude>
	<Longitude>145.snip</Longitude>
	<MetroCode>0</MetroCode>
</Response>
```

Keep that in mind, when we enable the VPN in a moment it’ll somewhere else. Install openvpn, then grab the PIA certs, if you are using a different VPN such as Nord, this step may be slightly different for you.

```bash
sudo apt-get install -y openvpn

cd /etc/openvpn
wget https://www.privateinternetaccess.com/openvpn/openvpn.zip
unzip openvpn.zip
```

Now lets configure OpenVPN, this is what I have inside /etc/openvpn/client.conf

```
client
dev tun
proto udp
remote nz.privateinternetaccess.com 1198
remote-random
resolv-retry infinite
nobind
persist-key
persist-tun
cipher aes-128-cbc
auth sha1
tls-client
remote-cert-tls server
comp-lzo
verb 1
reneg-sec 0
crl-verify crl.rsa.2048.pem
ca ca.rsa.2048.crt
disable-occ
keepalive 10 60
script-security 3
log /var/log/openvpn.log
auth-user-pass /etc/openvpn/auth
up /etc/openvpn/up.sh
down /etc/openvpn/down.sh
```

Most of these settings you can keep the same, but let me explain a few you’ll need to change

- remote; I have this set to the New Zealand PIA domain and port, you may like to choose a connection closer to your location.
- log; if anything goes wrong with OpenVPN, this is the log file you want to check.
- auth-user-pass; this points to a plain text file that contains your VPN login credentials. It should contain 2 lines, the first your username, and the second your password. This saves you having to type it in each time.
- up; when the VPN connects, this script gets run, we use this to start up the torrent daemon.
- down; when the VPN disconnects, this script gets run, we use this to stop the torrent daemon, you could also use it to send alerts if you like.

Lets have a look at those up and down scripts, they are mostly self explanatory, they invoke the transmission-remote, passing a username and password, asking the torrent daemon to either start or stop the torrents.

```
#!/bin/sh

echo "Starting Transmission Torrent Downloading"

transmission-remote --auth transmission:transmissionPassword --torrent all --start




#!/bin/sh

echo "Stopping Transmission Torrent Downloading"

transmission-remote --auth transmission:transmissionPassword --torrent all --stop






sudo chmod +x up.sh down.sh
```

Setting up and configuring Transmission
---------------------------------------

Now we get onto setting up the torrent client, transmission. Firstly install transmission and the daemon, then lets configure it.

```
sudo apt-get install transmission transmission-daemon -y
cd /etc/transmission-daemon
```

Next lets configure it, this is a copy of my settings.json, most can be left as defaults, but I’ll mention the params I changed below. The settings file is only writable by its owner, debian-transmission, so you’ll need to sudo nano it.

```json
{
    "alt-speed-down": 50,
    "alt-speed-enabled": false,
    "alt-speed-time-begin": 540,
    "alt-speed-time-day": 127,
    "alt-speed-time-enabled": false,
    "alt-speed-time-end": 1020,
    "alt-speed-up": 50,
    "bind-address-ipv4": "0.0.0.0",
    "bind-address-ipv6": "::",
    "blocklist-enabled": false,
    "blocklist-url": "http://www.example.com/blocklist",
    "cache-size-mb": 4,
    "dht-enabled": true,
    "download-dir": "/downloads/complete",
    "download-limit": 100,
    "download-limit-enabled": 0,
    "download-queue-enabled": true,
    "download-queue-size": 5,
    "encryption": 1,
    "idle-seeding-limit": 30,
    "idle-seeding-limit-enabled": false,
    "incomplete-dir": "/downloads/incomplete",
    "incomplete-dir-enabled": true,
    "lpd-enabled": false,
    "max-peers-global": 200,
    "message-level": 1,
    "peer-congestion-algorithm": "",
    "peer-id-ttl-hours": 6,
    "peer-limit-global": 200,
    "peer-limit-per-torrent": 50,
    "peer-port": 51413,
    "peer-port-random-high": 65535,
    "peer-port-random-low": 49152,
    "peer-port-random-on-start": false,
    "peer-socket-tos": "default",
    "pex-enabled": true,
    "port-forwarding-enabled": false,
    "preallocation": 1,
    "prefetch-enabled": true,
    "queue-stalled-enabled": true,
    "queue-stalled-minutes": 30,
    "ratio-limit": 2,
    "ratio-limit-enabled": false,
    "rename-partial-files": true,
    "rpc-authentication-required": true,
    "rpc-bind-address": "0.0.0.0",
    "rpc-enabled": true,
    "rpc-host-whitelist": "",
    "rpc-host-whitelist-enabled": true,
    "rpc-password": "{6f60be1c6d40ff65654325432542d02C1ixC8LB",
    "rpc-port": 9091,
    "rpc-url": "/transmission/",
    "rpc-username": "transmission",
    "rpc-whitelist": "127.0.0.1,192.168.*.*",
    "rpc-whitelist-enabled": true,
    "scrape-paused-torrents-enabled": true,
    "script-torrent-done-enabled": false,
    "script-torrent-done-filename": "",
    "seed-queue-enabled": false,
    "seed-queue-size": 10,
    "speed-limit-down": 100,
    "speed-limit-down-enabled": false,
    "speed-limit-up": 100,
    "speed-limit-up-enabled": false,
    "start-added-torrents": true,
    "trash-original-torrent-files": false,
    "umask": 2,
    "upload-limit": 100,
    "upload-limit-enabled": 0,
    "upload-slots-per-torrent": 14,
    "utp-enabled": true
}
```

- download-dir; the location you want the files to go to, I created a new directory from /.
- incomplete-dir; similar to download-dir, but for the incomplete files
- rpc-enabled; set that to true, it’s needed for the web UI and the sh scripts we created above, it allows us to interact with the daemon.
- rpc-password; note that my password is encrypted, to reset the password you can just type it in quotes, like “password”, then when the daemon starts it’ll encrypt it for you and save it back. Remember to update the up/down sh files if you change the password, they use this.
- rpc-port; change this if you already have something running on that port.
- rpc-whitelist; the hosts that can access the daemon, I’ve only exposed to my home network.

That should be it for transmission, just a few small tasks left now

```bash
# setup the download directories
sudo mkdir -p /downloads/complete
sudo mkdir -p /downloads/incomplete

# add the default pi user to the transmission group
sudo usermod -a -G debian-transmission pi

#make debian-transmission group own the download directories
sudo chown -R debian-transmission downloads
sudo chgrp -R debian-transmission downloads

# stop and start the transmission-daemon, if you check your settings.json you should see the plain text password now encrypted
sudo service transmission-daemon stop
sudo service transmission-daemon start
```

Starting up and using transmission
----------------------------------

Let’s go back into /etc/openvpn and create a script that we will use to start this all up.

```bash
sudo openvpn \
        --config /etc/openvpn/newzealand.ovpn \
        --auth-user-pass /etc/openvpn/auth \
        --up /etc/openvpn/up.sh \
        --down /etc/openvpn/down.sh \
        --script-security 2
```

- config; which VPN config file to use, this just points to the NZ ovpn files I downloaded from PIA.
- auth-user-pass; that auth file with the username on the first line and password on the second. This prevents the password from being in the script and easier to change.
- up; the script to run when the VPN first starts, in our case we link to up.sh which asks the transmission-daemon (via RPC) to start up and start it’s torrents.
- down; the inverse, when the VPN drops connection, we use this hook to tell transmission to stop its downloads.

All there is left is to start it, you should see this output:

```
./startVpn.sh

Thu Jan  2 09:32:32 2020 OpenVPN 2.4.7 arm-unknown-linux-gnueabihf [SSL (OpenSSL)] [LZO] [LZ4] [EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] built on Feb 20 2019
Thu Jan  2 09:32:32 2020 library versions: OpenSSL 1.1.1d  10 Sep 2019, LZO 2.10
Thu Jan  2 09:32:32 2020 NOTE: the current --script-security setting may allow this configuration to call user-defined scripts
Thu Jan  2 09:32:32 2020 TCP/UDP: Preserving recently used remote address: [AF_INET]103.231.91.34:1198
Thu Jan  2 09:32:32 2020 UDP link local: (not bound)
Thu Jan  2 09:32:32 2020 UDP link remote: [AF_INET]103.231.91.34:1198
Thu Jan  2 09:32:33 2020 [1339e49fdc4bef5b42eefc566390a9aa] Peer Connection Initiated with [AF_INET]103.231.91.34:1198
Thu Jan  2 09:32:34 2020 TUN/TAP device tun0 opened
Thu Jan  2 09:32:34 2020 /sbin/ip link set dev tun0 up mtu 1500
Thu Jan  2 09:32:34 2020 /sbin/ip addr add dev tun0 local 10.45.10.6 peer 10.45.10.5
Thu Jan  2 09:32:34 2020 /etc/openvpn/up.sh tun0 1500 1558 10.45.10.6 10.45.10.5 init
Starting Transmission Torrent Downloading
localhost:9091/transmission/rpc/ responded: "success"
Thu Jan  2 09:32:34 2020 Initialization Sequence Completed
```

That’s it, you should now be able to log into [http://myhostname:9091/transmission/web/](http://pibay:9091/transmission/web/) and start adding your torrent files. I’ve listed a few common issues below, if you have any questions leave a comment.

Common issues
-------------

- 401 Unauthorized user
  - Check /etc/transmission-daemon/settings.json for your rpc-password, make sure you start/stop the daemon for the change to take effect, [this post on SuperUser](https://superuser.com/a/113652) explains it well.
  - Check the up/down scripts are passing in the password (in plain text).
  - Check the rpc-whitelist in /etc/transmission-daemon/settings.json, I had to add my home network 192.168.\*.\*, it was returning a 401 as I was not whitelisted from my laptop.
- Unable to save file, permission denied
  - This is probably due to the permissions on the /downloads directory, double check that the /downloads is owned by debian-transmission and the group owns it too. Check steps above.

