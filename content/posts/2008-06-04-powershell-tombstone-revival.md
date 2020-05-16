---
categories:
- Tech Stuff
date: "2008-06-04T15:36:12Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6176150544"
status: publish
tags:
- AD
- GPOGuy
- PowerShell
- Windows Server
title: PowerShell TombStone revival
---
Our friend Darren Mar-Elia over at SDM software has just released some nice PowerShell cmdlets, to&nbsp; view Tombstoned objects in AD... You can even revive(or reanimate them as I think the correct AD term is) them as well.. The only requirements are PowerShell (duuh), .Net 2.0 and W2k3 running AD.

I installed it without any problems on a x86 system, but had a few minor problems running it on my 64bit Vista rig, here is what I did to get it working.

Creating an Alias for the .Net installutil, notice the **Framework64** here.  
[sourcecode lang="ps"]  
set-alias installutil $env:windir\Microsoft.NET\Framework64\v2.0.50727\installutil  
[/sourcecode]

I then installed the .dll file manually using the alias I just created above.  
[sourcecode lang="ps"]  
installutil -i "C:\Program Files (x86)\SDM Software\SDM Software AD Tombstone Cmdlets\ADTombstones.dll"  
[/sourcecode]

Mind you if you are running Vista, the regular user will not have access to "program Files" folder, and you will not be able to register the .dll without running as admin.

I then checked to see if the .dll actually had been loaded

[sourcecode lang="ps"]  
get-pssnapin -registered  
[/sourcecode]

I saw that it was succesfully added, I was then ready for the final thing, before I could set out on my revival quest.

[sourcecode lang="ps"]  
add-pssnapin SDMSoftware.PowerShell.AD.Tombstones  
[/sourcecode]

Take it for a spin, it is a nice little cmdlet.

