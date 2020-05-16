---
categories:
- Tech Stuff
date: "2009-09-20T09:40:42Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6099514464"
status: publish
tags:
- AD
- ADMGS
- PowerShell
title: ADMGS (Active Directory Management Gateway Service)
---
There has been some speculation on the web regarding the ADMGS for Server 2008 and 2003, if it would be possible to use it, in an environment without at least one Server 2008 R2.

So I decided to test it, I built a Server 2003 Domain, comprised of a single 2003 R2 Domain Controller, and 1 Windows 7 Enterprise workstation.  
I installed Server 2003 into a VM, installed all updates inlcuding SP2, I then ran DCpromo to setup the new domain. When that was complete I downloaded the 2 required patches for 2003, to be able to run ADMGS.  
(NDP35SP1-KB969166-x86.exe and 376193\_ENU\_i386\_zip.exe), where the last of the two patches requires registration to download.

When they were installed, I downloaded the ADMGS setup file (Windows5.2-KB968934-x86.exe).  
I created a few users, and everything worked fine.

I then built a Windows 7 client, downloaded the RSAT for Win 7 and joined it to the domain. I then ran my Powershell script to enable all RSAT tools on the machine.

The first thing I tried out was the new Active Directory Administrative Center, the new AD manament GUI built on Powershell, it connected to the DC, and I searched for the users I had created, and they showed up \*\*SUCCESS\*\*  
I played around with it some more, creating/deleting some users, and everything seemed to work fine, so I tried out the "Active Directory Module for PowerShell", pulling down some information about users and computers which also worked.

So all in all it seems as if it is not required to have a Server 2008 R2 in you domain/forest in order to use ADWS (Active Directory Web services) in your domain.

So there you have it folks, there are now a full live competitor to the Quest AD cmdlets, which have been around for a while and have let you manage you 2003 AD... Let the fight begin :)

