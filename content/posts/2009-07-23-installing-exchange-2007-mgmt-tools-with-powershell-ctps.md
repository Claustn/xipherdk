---
categories:
- Everyday
date: "2009-07-23T08:51:34Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6284231771"
status: publish
tags: []
title: Installing Exchange 2007 Mgmt tools with PowerShell CTP's
---
When you try to install Exchange 2007 mgmt tools on a machine running anything else than PowerShell V1 (yah yah I know, shouldn't run "beta" software in production, but I want my remoting).  
So I guessed it was checking some registry value, did a bit of digging and found it it was looking for this :

"HKLM:\SOFTWARE\Microsoft\PowerShell\1" PID "89383-100-0001260-04309"

So using PowerShell's Set-item it looks something like this

Set-Item "HKLM:\SOFTWARE\Microsoft\PowerShell\1" PID "89383-100-0001260-04309"

