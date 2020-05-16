---
categories:
- Tech Stuff
date: "2012-03-14T21:50:50Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6104286949"
status: publish
tags:
- AnyConnect
- Cisco
- Intel
- VPN
title: The mystery of why Cisco AnyConnect stopped working
---
A while back I tried to update my Intel Centrino Ultimate 6300 Wifi NIC to the latest drivers, to test Intel MY wifi while I was out travelling, but I never got it to work, it said it could not detect a suitable adaptor.. So I did not think more of that, until I had to connect to our corporate network, using Cisco AnyConnect, and I got through both primary and secondary authentication fine, but then the connection crapped out with "unable to establish vpn".. So after a few reboots and troubleshooting I ended up uninstalling the new driver, and reverting back to the old Lenovo one, I was running before.

Tonight I thought I would try again, to see if I could figure out what went wrong. So I installed the latest Intel drivers, and AnyConnect stopped working right away. So I went into Services to see what was "new" in there. I looked through all the Intel ones, and none of them looked "suspicious", until I noticed that right below the Intel services, was "Internet Connnection Sharing (ICS)" and it was running. So I disabled it, and AnyConnected connected right away.

So if you have trouble connecting with AnyConnect, see if your connection has ICS enabled, or if you do not need it at all, just disable the service.

