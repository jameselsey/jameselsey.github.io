---
title: 'Listing devices on your local network'
date: '2018-01-06T10:24:36+10:00'
permalink: /listing-devices-on-your-local-network
author: 'James Elsey'
category:
    - 'Raspberry Pi'
tag:
    - networking
    - raspberry-pi
---
Plugged my ancient Raspberry Pi in to my router (yeah the original, that doesn’t have on board wifi) and wanted to SSH into it, found this command to easily show you what devices are on your network, listing the IP address and the hostname

```
nmap -sL 192.168.1.* | grep \(1<br></br>Nmap scan report for D-Link.Home (192.168.1.1)<br></br>Nmap scan report for envoy (192.168.1.3)<br></br>Nmap scan report for Jamess-MBP (192.168.1.4)<br></br>Nmap scan report for LGSmartTV (192.168.1.5)<br></br>Nmap scan report for MeaganAir (192.168.1.9)<br></br>Nmap scan report for LGwebOSTV (192.168.1.10)<br></br>Nmap scan report for Meagans-iPhone (192.168.1.12)<br></br>Nmap scan report for retropie (192.168.1.13)<br></br>
```

There we go, last one on the list. The piping to grep just filters out addresses that are in use, otherwise it’d list all 255 addresses.