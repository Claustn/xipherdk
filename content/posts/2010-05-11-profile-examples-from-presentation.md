---
categories:
- Everyday
date: "2010-05-11T21:47:52Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
status: publish
tags:
- Pofiles
- PowerShell
title: Profile Examples from Presentation
---
Here are some of the scripts that I used during the demonstration in VA Beach.

Profiles:

Example #1

[ps]  
function prompt  
{  
"Xenophane " + $(get-location) + "\>"  
}  
[/ps]

Example #2

[ps]  
function prompt  
{  
"Processes " + (Get-Process).Count+ " " + $(get-location) + "\> "  
}  
[/ps]

Example #3 (Example taken from website)

[ps]  
function Prompt  
{  
 $id = 1  
 $historyItem = Get-History -Count 1  
 if($historyItem)  
 {  
 $id = $historyItem.Id + 1  
 }

Write-Host -ForegroundColor DarkGray `n[$(Get-Location)]  
 Write-Host -NoNewLine PS$id  
 $host.UI.RawUI.WindowTitle = $(Get-Location)

`b  
}  
[/ps]

Example #4 (Overdone one)

[ps]  
function prompt  
{  
[wmi]$Global:Cdrive=Get-WmiObject -query "Select \* from win32\_logicaldisk where deviceid='c:'"  
#Define Cdrive as global variable, which is type cast as a wmi  
$processcount=(Get-Process).Count  
#Define variable processcount as number of processes running  
$cpuload=(Get-WmiObject win32\_processor).loadpercentage  
#cpuload is defined as a variable  
$diskinfoC=" Free C: "+"{0:N2}" -f (($global:Cdrive.freespace/1gb)/($global:cdrive.size/1gb)\*100)+"%"  
#diskinfoC is defined, {0:N2} is .NET for 2 decimal places, -f is a the "format" property of the string object.  
# Then the amount of free space on drive C is divided by the size of drive C multiplies by 100 to get  
#the percentage of free space.  
$time=Get-WmiObject -class Win32\_OperatingSystem  
#Define $time is object Win32\_OperatingSystem  
$t=$time.ConvertToDateTime($time.Lastbootuptime)  
#Define #t as the result of the convertion of LastBootuptime into a readable dateTime format.  
[TimeSpan]$uptime=New-TimeSpan $t $(get-date)  
#Typecast the variable $uptime as a [timespan], the timespan cmdlet in powershell is a way to do date arithmetic in ps  
#This way we can compare our variable $t (Which was Lastbootuptime) and the current time Get-date cmdlet  
$up="$($uptime.days)d $($uptime.hours)h $($uptime.minutes)m $($uptime.seconds)s"  
#define variable $up to display the uptime in days/hours/minuted/seconds  
$TextToDisplay="CPU:"+$cpuload+"% Processes:" + $processcount + $diskinfoC+ " " + $diskinfoG+ " "+ ([char]0x25b2)+$up +" "+(Get-Date -format g)  
#define one variable that contains all the data we collected so far.  
Write-Host -fore red $TextToDisplay  
#Write the information to the screen in red  
Write-Host "My PoSh:" $(get-location) -nonewline  
#Display the regular prompt.  
}

[/ps]

