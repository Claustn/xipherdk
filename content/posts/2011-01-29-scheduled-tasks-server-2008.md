---
categories:
- Everyday
date: "2011-01-29T22:12:17Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6101958485"
status: publish
tags: []
title: Scheduled Tasks Server 2008(R2)
---
I have been working on a script for changing passwords on scheduled tasks in our domain, in that process I have run into a few kinks with scheduled tasks in server 2008(R2).

I tried different ways of manipulating the scheduled tasks, but ended up using schtasks.exe from the commandline, so I wrote some Powershell code to wrap around the schtasks.exe to connect to multiple machines.. But I kept getting errors on some servers, so I decided to dig into the issue. After a lot of testing I figured out, that scheduled tasks created from the Task Scheduler GUI, could not be changed with schtasks.exe from the commandline.   
So I had to dig a little deeper, and figure out what was happening, and how to work around it. The problem can be seen if you export the scheduled task into XML.

The first picture here is a scheduled task created from the command line.

[![image](/assets/images/image_thumb10.png "image")](http://www.xipher.dk/WordPress/wp-content/uploads/image10.png)

&nbsp;

This one is from a scheduled task created from the Task scheduler GUI (Notice the date format contains 6 or 7 decimals)

[![image](/assets/images/image_thumb11.png "image")](http://www.xipher.dk/WordPress/wp-content/uploads/image11.png)

Apparently schtasks.exe does not like the date/time format in a task created in the GUI, but if you export the scheduled task to XML change remove the “decimals” and reimport the scheduled task, you can now manage it from the command line with schtasks.exe (How was this not caught in testing ??)

&nbsp;

So to avoid going out to all 2008(R2) machines and do it manually, I wrote a small Powershell script to dump a scheduled task to XML and change the date/time and reimport it.

[ps]  
Function Update-ScheduledTask {  
param(  
 [Parameter(Position=1,Mandatory=$true)]  
 $SchedName ,  
 [Parameter(Position=2,Mandatory=$false)]  
 $ComputerName = "Localhost"  
 )

$ShortDate = [regex]"(?\<ShortDate\>[0-9]{4}[/.-](?:1[0-2]|0[1-9])[/.-](?:3[01]|[12][0-9]|0[1-9])T(?:2[0-3]|[01][0-9])[:.][0-5][0-9][:.][0-5][0-9])(?\<Digits\>\.\d\*)"  
Remove-Item "$Env:TEMP\$($SchedName).xml"  
$XMLIn = schtasks /query /s $Computername /tn $SchedName /xml

foreach ($line in $XMLIn){  
If ($line -match "$ShortDate") {  
$line = [regex]::Replace($line, $ShortDate, $($Matches["Shortdate"]))  
}  
If ($line.length -gt 1) {  
$line | Out-File -Append -FilePath "$Env:TEMP\$($SchedName).xml"}  
}  
schtasks /Create /tn $SchedName /XML "$Env:TEMP\$($SchedName).xml" /f  
}  
 [/ps]

