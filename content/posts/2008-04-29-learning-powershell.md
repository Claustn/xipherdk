---
categories:
- Tech Stuff
date: "2008-04-29T13:13:47Z"
meta:
  _edit_last: "1"
  dsq_thread_id: "6099514344"
status: publish
tags:
- PoSH
- PowerShell
- Scripting
title: Learning PowerShell
---
Don Jones' keynote basically said, if you do not learn PowerShell, you will not be able to function as an Windows IT administrator in 5 years time or so... After what was told on the MVP summit, about Microsofts commitment to PowerShell, and how all new products will be based on it, he foresees are very dim future for admins who do not know it.

One of his suggestions was to read a PowerShell help file every day... Just go through the help files one by one, you will most&nbsp; likely not remember all of it, but if you read enough, some point might stick ;-)

So on the way home I decided to try to write a little PowerShell script that would display a help file, and automatically store the the number of the helpfile that I have reached. (So I do not have to keep track of it myself)

This is the most basic version, I came up with it on the plane home from the Forum Meet.

``

[int]$HelpNr = Get-Content -Path c:\scripts\test.ini  
$vhelp = Get-Help \* | Get-Help  
$i=0  
 foreach ($vvhelp in $vhelp){  
 $i++  
 if ($i -eq $HelpNr) {$vvhelp}  
 }  
 $HelpNr++  
Set-Content -Path c:\scripts\test.ini -Value $HelpNr

It has to be said though, that this is hardcoded to look for a file "c:\scripts\test.ini " which is where it keeps track of the number of help files you have gone through.  
If you are going to test the program, in this version you have to create the .ini file, and just put a 1 in the first line of it.

