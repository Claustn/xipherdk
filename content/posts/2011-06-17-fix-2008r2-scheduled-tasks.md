---
categories:
- Everyday
date: "2011-06-17T21:29:00Z"
meta:
  _edit_last: "1"
  _syntaxhighlighter_encoded: "1"
  dsq_thread_id: "6227559709"
status: publish
tags: []
title: Fix 2008R2 scheduled tasks
---
[ps]  
Function Change-ScheduledTask {  
Param($XMLIn)  
$ShortDate = [regex]"(?\<shortdate\>[0-9]{4}[/.-](?:1[0-2]|0[1-9])[/.-](?:3[01]|[12][0-9]|0[1-9])T(?:2[0-3]|[01][0-9])[:.][0-5][0-9][:.][0-5][0-9])(?\<digits\>\.\d\*)"

foreach ($line in $XMLIn){  
If ($line -match "$ShortDate") {  
$line = [regex]::Replace($line, $ShortDate, $($Matches["Shortdate"]))  
}  
$line  
#$Script:outfile += $line + "`n"  
}  
}

Function Remove-ScheduledTask {

}

Function New-ScheduledTask {

}

$schtask = Schtasks.exe /query /s "localhost" /V /FO CSV | ConvertFrom-Csv

if ($schtask) {

Foreach ($sch in $schtask) {

if ($Sch."Run As User" -like "$($domainname)\*") {  
$sch  
}  
}

$outfile = ""  
Change-scheduledtask $(Get-Content c:\temp\TestTask.xml)  
[/ps]

