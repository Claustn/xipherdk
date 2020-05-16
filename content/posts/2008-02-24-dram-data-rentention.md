---
categories:
- Tech Stuff
date: "2008-02-24T23:51:59Z"
meta: null
status: publish
tags: []
title: DRAM data rentention
---
Researchers from Princeton recently published an article about the possibility to extract information from RAM even after the PC has been turned on.

In short, modern RAM contrary to popular belief does not instantly loose its contents, it degrades gradually over time.  
What does this mean ?

One security issue that could arise from the fact that most disk encryption software keeps the encryption key and pre computed key schedules in memory for performance reasons, meaning that if an attacker gains access to the machine he can copy these for offline attacks.

The article further tests the influence of low temperatures to prevent rapid decay of data in the RAM, they cooled the RAM modules to -50 centigrade, which lead to less than 1% data degradation over 5 min.

They even tried cooling a module down with liquid nitrogen and observed a 0.17% decay after 60 minutes.

You can read the full [article](http://arstechnica.com/news.ars/post/20080221-researchers-crack-filevault-bitlocker-with-canned-air-hack.html) here.

