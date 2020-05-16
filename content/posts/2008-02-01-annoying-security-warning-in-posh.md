---
categories:
- Tech Stuff
date: "2008-02-01T12:25:46Z"
meta:
  dsq_thread_id: "6107561663"
status: publish
tags: []
title: Annoying Security warning in Posh
---
As I wrote in an earlier post I am using PowerTabs in Powershell.. I recently upgraded to version 0.99 Beta 2 and it would come up with:

> Security WarningRun only scripts that you trust. While scripts from the Internet can be useful, this script can potentially harm yourcomputer. Do you want to run foo.ps1?[D] Do not run [R] Run once [S] Suspend [?] Help (default is "D"):

I got this every time I &nbsp;started PowerShell because it was loading the PowerTab files in my PowerShell profile.  
I know this is not good practice, but my Execution Policy on my test machine was set to "unrestricted", so I was wondering why I still got the error. If I changed my Execution policy to RemoteSigned I got the familiar

> File C:\Users\ctn\Documents\WindowsPowerShell\PowerTab\Init-TabExpansion.ps1 cannot be loaded. The file C:\Users\ctn\Documents\WindowsPowerShell\PowerTab\Init-TabExpansion.ps1 is not digitally signed. The script will not execute on the system. Please see "get-help about\_signing" for more details..  
> At C:\Users\ctn\Documents\WindowsPowerShell\Microsoft.PowerShell\_profile.ps1:16 char:2  
> + & \< \<\<\< 'C:\Users\ctn\Documents\WindowsPowerShell\PowerTab\Init-TabExpansion.ps1' `

That made me remember something Mark said about Vista tagging all files to see if they are internet files or local files, trusted or untrusted.

So I looked into where Vista stores this information (If my memory was better I am sure Mark has said that as well). So I downloaded Streams from Sysinternals to see if something was stored in the stream of the files.

I did a  
`streams.exe c:\Users\ctn\Documents\WindowsPowerShell\PowerTab\Init-TabExpansion.ps1`  
Which resulted in:

> Streams v1.56 - Enumerate alternate NTFS data streams  
> Copyright (C) 1999-2007 Mark Russinovich  
> Sysinternals - www.sysinternals.com
> 
> c:\Users\ctn\Documents\WindowsPowerShell\PowerTab\Init-TabExpansion.ps1:  
> :Zone.Identifier:$DATA 26

Low and behold the PowerTab files have a 26 byte "Zone.Identifier:$Data" stream.

I opened the stream to see what it... This can be done with good ol' trusty notepad  
`Notepad File-Name.ps1:Zone.Identifier`

Notepad showed the following:

> [ZoneTransfer]  
> ZoneId=3

I thougt ZoneID=3 ??? I consulted the Internet I found the following table.

> NoZone = -1  
> MyComputer = 0  
> Intranet = 1  
> Trusted = 2  
> Internet = 3  
> Untrusted = 4

I then changed the ZoneID value to 2(trusted) and I do no longer get the annoying security warning.

Just for fun I tried to change my execution policy to RemoteSigned, and it still executed the script without any trouble.

> 

